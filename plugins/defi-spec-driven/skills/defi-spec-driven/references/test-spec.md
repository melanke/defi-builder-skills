# Phase 6 — Test Specification
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Specify the tests, not write them. Define what needs to be verified and in what form, before touching test code.

Load: `STATE.md`, `economics/INVARIANTS.md`, `interface/FUNCTIONS.md`, `threat-model/THREAT-MODEL.md`
Output: `tests/FUZZ-TARGETS.md`, `tests/SCENARIOS.md`, `tests/UNIT-TESTS.md`

The output of this phase feeds directly into project setup: `FUZZ-TARGETS.md` → stubs in `Properties.sol`; `SCENARIOS.md` → stubs in `test/integration/`; `UNIT-TESTS.md` → stubs in `test/unit/`; symbolic entries in `FUZZ-TARGETS.md` → `check_` stubs in `test/symbolic/`.

---

## Core principle

Resist the instinct to cover every possible path. A contract with 200 passing unit tests can still have an economic vulnerability that none of them imagined — unit tests test what you thought to test, and the adversarial actor isn't constrained by your test cases.

**The composition that works**:
- **Fuzz/invariant tests** — the primary security layer. Defined by the INV-* slugs from Phase 2
- **Symbolic proofs** — formal verification for pure arithmetic invariants. A passing `check_` function is a proof over all inputs, not a probabilistic result
- **Scenario tests** — for known attack patterns and multi-step flows, written in Gherkin
- **Unit tests** — minimal. The most critical happy paths and a handful of obvious reverts. Treated as documentation, not the security net

---

## Step 1: Fuzz test targets (FUZZ-TARGETS.md)

Every invariant defined in Phase 2 becomes a fuzz test target. The list here is a direct mapping from `INVARIANTS.md`.

For each INV-* entry, add a `Classification` field — this determines where the test lives:

| Classification | Criteria | Where it lives |
|---|---|---|
| `fuzz` | Stateful, depends on call sequences, involves external calls | `property_` in `Properties.sol` |
| `symbolic` | Pure arithmetic relationship between state variables, checkable in a single state snapshot, no external calls | `check_` in `test/symbolic/[Contract]Proof.t.sol` |
| `both` | Simple enough for Halmos AND benefits from call-sequence exploration | Both |

**Symbolic classification rule**: if you can express the invariant as a closed-form assertion over a single contract state (e.g., `assert(vault.totalShares() == 0 || vault.totalAssets() > 0)`), it's a symbolic candidate. If it requires a history of calls to make sense, it's fuzz-only.

Every invariant gets its own `##` section — no `###`, no nesting, no exceptions. Semantic relationships between invariants belong in the body text, not in the heading hierarchy.

```markdown
# Fuzz Test Targets

## INV-vault-solvency → FUZZ-vault-solvency
**Invariant**: totalAssets >= sum of all redeemable user claims at all times
**Classification**: fuzz — depends on deposit/withdraw call sequences
**Fuzz strategy**: stateful fuzzing — call deposit(), withdraw(), transfer() in arbitrary sequence with arbitrary values
**Property to assert after every call**: vault.totalAssets() >= sum of all shares * sharePrice
**Edge cases to seed**: zero deposit, max uint256 deposit, full withdrawal after donation

## INV-share-supply-nonzero → FUZZ-share-supply-nonzero
**Classification**: both — arithmetic check AND useful as fuzz invariant after any call
**Symbolic**: check_share_supply_nonzero() in test/symbolic/VaultProof.t.sol
[...]
```

**Implicit invariant pass**: Before closing this list, do one more dedicated pass to surface invariants that fall out of the design but were never explicitly stated. Ask the LLM to review the complete spec (INVARIANTS.md + FUNCTIONS.md) and identify any invariants that weren't declared. They're easy to miss precisely because they feel obvious.

---

## Step 2: Attack scenario specs (SCENARIOS.md)

Gherkin fits naturally here. Given/When/Then makes pre-conditions explicit and produces something an auditor can read alongside the test code.

**Source for scenarios**: The attack vectors documented in Phase 4 `THREAT-MODEL.md`. Every documented attack vector with a mitigation should have a corresponding scenario test that verifies the mitigation works.

```gherkin
# SCENARIOS.md

Scenario: Flash loan attack on vault pricing
  Given a vault with 1 ETH initial deposit (dead shares initialized)
  When an attacker takes a flash loan of 100 ETH
  And deposits 100 ETH into the vault
  And attempts to withdraw at the inflated share price
  And repays the flash loan in the same transaction
  Then the attacker has extracted zero profit
  And the vault totalAssets is identical to before the attack

Scenario: Reentrancy attempt on withdraw
  Given a user holding vault shares
  And the user's address is a contract that calls withdraw() again on receive
  When the user calls withdraw()
  Then the second withdraw call reverts
  And the user receives exactly their entitled assets (no double withdrawal)
  And INV-vault-solvency holds after both calls settle

Scenario: Oracle manipulation during liquidation
  Given a lending position at 80% LTV
  When an attacker manipulates the spot price oracle to 50% of real value
  And calls liquidate() on the position
  Then the transaction reverts because the TWAP check fails
  And the position remains open
```

Each scenario maps to one or more threat model entries. Reference the attack vector slug if named.

**Completeness check**: After listing scenarios, cross-reference every entry in `THREAT-MODEL.md` and confirm each has a corresponding scenario. It is easy to write general scenarios (deposit, withdraw) and miss edge cases that have their own threat entries — e.g. force-ETH injection via `selfdestruct`, first-depositor inflation, zero-amount calls. Count the threat model entries, count the scenarios, and close any gaps before marking this step done.

**Gherkin to test code**: Describe the scenario in Gherkin, then ask the LLM to translate it to Foundry test code. Verify the assertions — you are checking that what the test calls "success" matches what the spec promises.

---

## Step 3: Unit test specs (UNIT-TESTS.md)

Unit tests are minimal — written as documentation of the expected behavior, not as the security net.

```gherkin
# UNIT-TESTS.md

Scenario: User deposits and receives correct shares (REQ-deposit-min-shares)
  Given a vault with 100 ETH total assets and 100 shares outstanding
  When a user deposits 10 ETH
  Then the user receives 10 shares (10% of new total)
  And totalAssets is 110 ETH
  And totalShares is 110
  And a Deposit event is emitted with correct parameters

Scenario: Withdrawal reverts when shares insufficient (REQ-withdraw-solvency)
  Given a user holds 10 shares
  When the user calls withdraw() with 11 shares
  Then the transaction reverts with InsufficientBalance(11, 10)

Scenario: Unauthorized caller cannot change fees (REQ-fee-admin-role)
  Given an address that does not hold the FeeAdmin role
  When it calls setFeeBps(500)
  Then the transaction reverts with UnauthorizedCaller
```

Map each scenario to its REQ-* slug. Keep this list short — if you find yourself writing unit tests for every function branch, stop and convert the important ones to scenario tests with explicit attack framing.

---

## Step 4: Symbolic proof stubs (test/symbolic/)

For every INV-* classified as `symbolic` or `both` in FUZZ-TARGETS.md, define a `check_` function stub. This is a spec entry — the actual Solidity is written during project setup.

```markdown
# Symbolic Proof Stubs

## check_share_supply_nonzero — INV-share-supply-nonzero
**Contract**: VaultProof.t.sol
**Assertion**: vault.totalShares() == 0 || vault.totalAssets() > 0
**Bounds**: no loops, no external calls — tractable for Halmos
**vm.assume guards**: none needed — assertion is unconditional

## check_deposit_shares_positive — INV-deposit-nonzero-shares
**Contract**: VaultProof.t.sol
**Assertion**: for any assets_ > 0, previewDeposit(assets_) > 0
**Bounds**: pure math — Halmos can prove over all uint256 inputs
**vm.assume guards**: vm.assume(assets_ > 0); vm.assume(totalSupply < type(uint256).max)
```

A `check_` function is tractable when: (1) the assertion references only state variables and pure math, (2) no external calls within the check itself, (3) no unbounded loops. When Halmos times out on a check, add `vm.assume()` to tighten the input space or split into smaller sub-proofs — don't delete the check.

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

**OQ gate** — before closing Phase 6, check `STATE.md` for any open question still unresolved. Each must be either CLOSED (decision recorded) or explicitly converted to ACCEPTED-AMBIGUITY (assumption + risk documented). No OQ survives Phase 6 silently.

- All open questions are CLOSED or ACCEPTED-AMBIGUITY in `STATE.md`
- `FUZZ-TARGETS.md` has an entry for every INV-* from Phase 2, each with a `Classification` field
- Every `symbolic` or `both` invariant has a `check_` stub defined in this phase
- `SCENARIOS.md` has a scenario for every attack vector in the threat model — count entries in `THREAT-MODEL.md` and confirm the count matches
- `UNIT-TESTS.md` covers the critical happy paths and the most important revert cases, with REQ-* references
- Every test spec entry can be handed to a developer (or an LLM) and produce unambiguous test code
- `STATE.md` Phase History updated: `[x] Phase 6 — Test Specification`

Transition: "Phase 6 complete. Spec is done. Say **implement protocol** to begin the implementation phase."
