# Implementation Task Tracking

**Goal**: Track per-function implementation progress against the spec. Every function goes through a defined loop: load spec → implement → verify → commit.

---

## Creating IMPL-TASKS.md

At the start of implementing a contract, create `.specs/impl/[ContractName]-IMPL-TASKS.md`:

```markdown
# [ContractName] — Implementation Tasks

| Function | Complexity | REQ-* | INV-* | Status | Gate | Deviation |
|---|---|---|---|---|---|---|
| initialize() | complex | REQ-init-dead-shares | INV-share-supply-nonzero | pending | — | — |
| deposit(uint256,address) | complex | REQ-deposit-min-shares, REQ-deposit-solvency | INV-vault-solvency | pending | — | — |
| withdraw(uint256,address,address) | complex | REQ-withdraw-solvency | INV-vault-solvency, INV-share-monotonic | pending | — | — |
| totalAssets() | simple | REQ-total-assets-accuracy | INV-vault-solvency | pending | — | — |
| pause() | simple | REQ-pause-access | — | pending | — | — |

## Complexity Levels
- `simple` — view functions, pure functions, admin setters with no state machine complexity → fast path
- `medium` — state-changing, 1-2 external calls, standard CEI pattern → standard loop
- `complex` — multi-step state mutations, multiple external calls, or functions with multiple Phase 4 threat entries → full loop

## Status Legend
- `pending` — not started
- `in-progress` — being implemented
- `gate-pending` — implemented, tests being written
- `verified` — gate check passed, no deviations
- `deviated` — implemented with SPEC_DEVIATION marker; permanent status regardless of whether spec has been updated
```

Pull the function list and REQ-*/INV-* mappings from `interface/FUNCTIONS.md`. Assign complexity based on:
- **simple**: no external calls, no state machine, minimal or no threat model entry
- **medium**: 1-2 external calls, clear CEI path, 1-2 threat vectors
- **complex**: appears in Phase 4 threat model with multiple attack vectors, or has cross-contract interactions, or manages critical invariants

---

## Execute loops by complexity

### Fast path — `simple` functions

1. Load REQ-* entry from `interface/FUNCTIONS.md` (skip threat model — minimal or empty)
2. Implement the function
3. Gate check: `forge build` (must compile clean) + 1 unit test (happy path + one obvious revert)
4. No fuzz test required, no scenario test required
5. Commit

`simple` functions should not take more than one focused session. If a "simple" function turns out to have unexpected complexity during implementation, reclassify it to `medium` or `complex` before continuing.

### Standard loop — `medium` functions

1. Load REQ-* entry from `interface/FUNCTIONS.md` + threat model entry
2. Implement against spec
3. Write: 1 unit test (happy path + critical reverts) + scenario test if threat model entry exists
4. Gate check: `forge build` + `forge test`
5. Commit

### Full loop — `complex` functions

Run this loop for every `complex` function:

#### 1. Load the spec

Before writing any code, read:
- The REQ-* entry for this function from `interface/FUNCTIONS.md` (pre/post-conditions, reverts, state mutations)
- The threat model entry for this function from `threat-model/THREAT-MODEL.md`

Update IMPL-TASKS.md: `pending` → `in-progress`

#### 2. Implement

Write the function against its spec. Use [implement.md](implement.md) for prompting guidelines.

If something during implementation diverges from the spec:
- Add `// SPEC_DEVIATION: REQ-*` comment in the code
- Note the deviation in IMPL-TASKS.md Deviation column
- Continue; update spec after the gate check passes

If you discover undocumented behavior:
- Add `// UNDOCUMENTED CODE PATH: [description]` comment
- Log it in `STATE.md` as a spec gap to close before audit

#### 3. Write the tests

For this function:
- Write the fuzz test targeting the relevant INV-* from `tests/FUZZ-TARGETS.md`
- Write the scenario test(s) from `tests/SCENARIOS.md` that cover its attack vectors
- Write the unit test(s) from `tests/UNIT-TESTS.md` for its happy path and critical reverts

Each test must reference the slug it's verifying: `// REQ-withdraw-solvency` or `// INV-vault-solvency`

Update IMPL-TASKS.md: `in-progress` → `gate-pending`

#### 4. Gate check

```bash
forge build          # must compile clean
forge test           # all tests for this function must pass
```

If gate fails: fix before proceeding. Do not move to the next function with a failing gate.

If the toolchain is unavailable (e.g. CI not configured yet, no compiler in environment): log a blocker in `STATE.md`, set gate column to `blocked — toolchain unavailable`, and add an entry to `KNOWN_ISSUES.md`. Do not invent alternative gate statuses. The gate must be run before marking the function `verified`.

Update IMPL-TASKS.md: `gate-pending` → `verified` (or `deviated` if SPEC_DEVIATION was marked)

#### 5. Commit atomically

Create a local git commit even if no remote is configured — the commit history is the audit trail. Push separately when a remote exists.

Commit message format:
```
feat(ContractName): implement [functionName] per REQ-*

- Implements REQ-withdraw-solvency, REQ-withdraw-shares-check
- Protects INV-vault-solvency via CEI + nonReentrant
- Tests: fuzz(INV-vault-solvency), scenario(flash-loan-attack), unit(withdraw-happy-path)
```

One commit per function (or per closely-related group of functions). Group only functions that share the same invariants and have no independent test surface.

If `git commit` fails because there is no git repository: run `git init && git add -A && git commit -m "chore: init"` to create one before committing the function.

---

## Contract completion

A contract is complete when all functions in IMPL-TASKS.md are `verified` or `deviated`.

Before marking a contract complete:
- All `deviated` entries must have the spec updated
- All UNDOCUMENTED CODE PATH markers must be logged in STATE.md
- Run `forge test` on the full contract suite — all tests pass

After completion: run the audit skill on this contract before starting the next one. Earlier findings = cheaper fixes.

## Done when — gate

Before marking implementation complete, name the path of each artifact. If you cannot name it, create it now.

- `.specs/impl/[ContractName]-IMPL-TASKS.md` exists and all functions are `verified` or `deviated`
- All `deviated` entries have the spec updated and a SPEC_DEVIATION comment in source
- All UNDOCUMENTED CODE PATH markers are logged in STATE.md
- `forge test` passes — run it and confirm
- Audit passes run: spec-to-code-compliance and solidity-auditor invoked (or explicitly deferred with reason)
