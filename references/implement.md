# Implementation Phase
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Write production Solidity aligned with the spec. Every function is implemented against its REQ-* slugs. Every deviation is marked explicitly.

Load: `PROTOCOL.md`, `STATE.md`, `interface/FUNCTIONS.md`, `threat-model/THREAT-MODEL.md`, `AGENTS.md` (project root)
Also load per-contract: `IMPL-TASKS.md` for the contract being implemented
See: [impl-tasks.md](impl-tasks.md) for the per-function execute loop

`AGENTS.md` contains the toolchain-specific instructions for this project: exact gate check commands per complexity level, how to fix `forge fmt`/`lintspec`/`slither` findings, and how to maintain `INVARIANTS.md`, `Properties.sol`, `CryticToFoundry.sol`, `BeforeAfter.sol`, and `KNOWN_ISSUES.md`. Read it before starting the first function.

---

## How to prompt the LLM for implementation

The spec transforms AI-assisted coding from "generate code and hope" into a reviewable, directed process.

**Wrong prompt**: "Write me a vault contract."

**Right prompt**: "Implement the `withdraw(uint256 shares)` function for this ERC-4626 vault according to these pre-conditions, post-conditions, and state mutations: [paste REQ-withdraw-solvency from FUNCTIONS.md]. The function must be resilient against these specific attack vectors: [paste THREAT-MODEL.md entry for withdraw()]. Use CEI pattern and nonReentrant guard."

The specificity of the prompt is directly proportional to the reviewability of the output. With a precise spec, you can verify that the implementation matches the stated contract — rather than guessing what the code is supposed to do.

---

## SPEC_DEVIATION marker

When implementation diverges from the spec — because you discovered something during coding that the spec didn't anticipate — mark it explicitly in the code:

```solidity
// SPEC_DEVIATION: REQ-withdraw-solvency
// Spec assumed CEI pattern sufficient. Added nonReentrant due to ERC-777 token
// compatibility (external call before state update is unavoidable for ERC-777 hooks).
// Spec updated: 2025-03-10
```

The `SPEC_DEVIATION` tag must reference the **REQ-* slug** from `interface/FUNCTIONS.md`, not a file name or free text. `// SPEC_DEVIATION: REQ-withdraw-solvency` is correct. `// SPEC_DEVIATION: FUNCTIONS.md` or `// SPEC_DEVIATION: withdraw` is not — auditors use these tags to cross-reference the spec entry directly.

Then update the spec to reflect the decision, with the reasoning intact.

**Rules**:
- Mark the deviation in code first, then update the spec
- Never silently update either without marking the gap
- Auditors treat SPEC_DEVIATION markers as high-priority review points — that's the intent
- Update IMPL-TASKS.md status to `deviated` for this function

---

## UNDOCUMENTED CODE PATH marker

For logic that the spec doesn't mention at all — not a deviation from stated behavior, but behavior that was never stated:

```solidity
// UNDOCUMENTED CODE PATH: zero-share protection on first deposit
// Not in spec. Discovered during implementation: share inflation attack is possible
// if first deposit can set share price arbitrarily. Minting 1000 dead shares to
// dead address on initialization. Spec to be updated before audit.
```

An UNDOCUMENTED CODE PATH is a spec gap, not a code error. Flag it, note the reason, and update the spec before the audit.

---

## Where auditing fits

After implementing each contract, run two audit passes before moving to the next contract. Earlier feedback means cheaper fixes.

**Pass 1 — Spec conformance**: fetch `https://raw.githubusercontent.com/trailofbits/skills/main/plugins/spec-to-code-compliance/skills/spec-to-code-compliance/SKILL.md` and follow its instructions. Inputs: `.specs/interface/FUNCTIONS.md`, `.specs/threat-model/THREAT-MODEL.md`, `src/[ContractName].sol`. Save findings to `.audit/[ContractName]-spec-compliance.md`.

**Pass 2 — Vulnerability finding**: fetch `https://raw.githubusercontent.com/pashov/skills/main/solidity-auditor/SKILL.md` and follow its instructions. Input: `src/[ContractName].sol`. Save findings to `.audit/[ContractName]-vulnerability.md`.

After both passes, read the two `.audit/` files and process each finding:
- If a finding maps to a SPEC_DEVIATION: update the spec with the finding's analysis
- If a finding reveals an UNDOCUMENTED CODE PATH: document it and fix it
- If a finding reveals a spec gap: update the threat model in Phase 4 before fixing the code
- If no findings: confirm and proceed to the next contract

After the full protocol is implemented, run a third pass:

**Pass 3 — Full protocol audit**: fetch `https://raw.githubusercontent.com/pashov/skills/main/solidity-auditor/SKILL.md` and follow its instructions against the entire `src/` directory. Save findings to `.audit/full-protocol-audit.md`. Run once at protocol completion, not per-contract — treat as a gate before engaging an external audit firm. Process findings the same way.

---

## Code review acceleration

A reviewer working from the spec can check implementation against stated behavior directly — they're not reconstructing intent from code. Discrepancies jump out.

When requesting code review, provide the relevant spec entries alongside the code diff. The review becomes: "does this implementation match REQ-withdraw-solvency?" rather than "what was this supposed to do?"

## Done when — gate

Before marking implementation complete for this session, name each artifact. If you cannot name it, it does not exist.

- `.specs/impl/[ContractName]-IMPL-TASKS.md` exists — name its path
- Every function in IMPL-TASKS.md is `verified` or `deviated` — state the count
- `forge build` output: "Compiler run successful!" with no warnings, no lint findings, no other output — run it and confirm
- `forge test` passes — run it and confirm
- Audit Pass 1 (spec-to-code-compliance) run and findings addressed (or explicitly deferred)
- Audit Pass 2 (solidity-auditor) run and findings addressed (or explicitly deferred)
