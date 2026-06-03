# Phase 2 — Economic Design Spec
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Define the mathematical invariants and economic model of the protocol on paper, before any code.

Load: `PROTOCOL.md`, `STATE.md`, `research/RESEARCH.md`
Output: `economics/INVARIANTS.md`, `economics/ECONOMICS.md`

---

## Core principle

If you cannot write the invariants of your protocol as precise mathematical statements before coding, you are not ready to code. The invariants are not documentation — they are formal claims about what the protocol promises to users. They become the backbone of fuzz testing in Phase 6 and the first line of defense against economic exploits.

---

## Step 1: Define the invariants — in rounds

An **invariant** is a property of the system that must always be true, regardless of the sequence of operations that led to the current state.

**Do not present all invariant candidates at once.** Work in groups of 2–3, waiting for confirmation before moving to the next group. The goal is to build the invariants together, not to present a list for approval.

**Round structure:**

**Group 1 — Core promise invariants**: The 2–3 invariants that fall directly from what the protocol promises to users. These are usually obvious from the protocol description. Start here.

For each invariant in the group: explain where it comes from, present the candidate, then ask for confirmation with a "why this matters" inline. Example:

*"The protocol promises that the floor never drops — this becomes INV-floor-monotonic. Confirm it is absolute, no governance exception? — The answer determines whether the fuzz test covers only the normal path or needs to test privileged calls as well."*

When an objection comes in during invariant confirmation, address it, incorporate the mitigation, and then ask explicitly: *"Does that make sense? Any other objections before closing this invariant?"* — don't wait for the developer to raise the next objection unprompted. This closes the loop actively rather than leaving the developer to decide when the invariant is "done enough".

**Group 2 — Operational invariants**: Invariants about who can do what (access control), supply integrity, and state transitions that must hold. Usually 2–4 invariants.

Same pattern: source → candidate → confirmation with "why this matters inline". Example of the confirmation line: *"Confirm that no external address can remove liquidity from the floor? — Determines whether beforeRemoveLiquidity needs an allowlist or whether an unconditional revert is sufficient."*

**Group 3 — Implicit invariants**: Properties the system guarantees by construction that were never explicitly stated. These are the ones most likely to be missed. Before this group, ask the LLM to review the full protocol description and surface invariants that weren't declared. Easy to miss precisely because they feel obvious.

For implicit invariants, the "why" in the confirmation must name the failure mode — not the protocol requirement. The difference:

- Weak (protocol requirement): *"Confirm? — The price→tick conversion requires alignment in Uni v4."*
- Strong (failure mode): *"Confirm? — If the calculated tick is not a multiple of tickSpacing, the PoolManager reverts the entire ratchet operation — we need an explicit fuzz target for this."*

The stronger form is what makes developers realize the invariant matters, instead of feeling like a formality.

After each group is confirmed, transition naturally: *"Group [N] confirmed. Moving to the next — say **continue** or reply directly."* Don't require an empty acknowledgement turn.

After all groups are confirmed: preview before writing, then highlight after.

**Before writing INVARIANTS.md**: one sentence naming what the file will contain and any inferences made during the invariant rounds.

Example: *"Writing INVARIANTS.md with [N] confirmed invariants — [INV-X] includes the ratchet atomicity clause you added, and [INV-Y] is marked implicit. Ok?"*

**After writing INVARIANTS.md**: don't ask "any adjustments?". Name the 1–2 invariants that have open decisions or that were the hardest to pin down, so the developer knows where to focus the review.

Example: *"INVARIANTS.md written. Worth revisiting: [INV-X] — the atomicity clause you added needs to appear explicitly in the Phase 4 threat model; [INV-Y] — still depends on the TWAP window that is open in STATE.md."*

Then: write `INVARIANTS.md`.

---

**Invariant format**:

```
INV-{noun}-{property}
Statement: [Formal mathematical or logical statement of the invariant]
In plain language: [What this means for users]
Breaks when: [Under what conditions could this be violated — for verification thinking]
Property function: property_{noun}_{property}() in test/recon/Properties.sol
Phase 6 reference: FUZZ-INV-{noun}-{property}
```

The `Property function` field is the explicit link between the spec and the fuzzer. The function name is derived mechanically from the slug: `INV-vault-solvency` → `property_vault_solvency()`. During project setup, stubs are generated in `test/recon/Properties.sol` from these entries — one stub per INV-* in this file.

**Examples by category**:

For a token vault (ERC-4626 pattern):
```
INV-vault-solvency
Statement: totalAssets >= sum of all redeemable user claims at any point in time
In plain language: the vault can always honor redemptions
Breaks when: totalAssets decreases without a proportional decrease in total shares

INV-share-supply-nonzero
Statement: totalShares > 0 iff totalAssets > 0
In plain language: shares only exist when there are assets backing them
Breaks when: assets are added before shares are initialized, or shares remain after full withdrawal

INV-deposit-share-monotonic
Statement: for any deposit d > 0, the shares minted must be > 0
In plain language: no deposit results in zero shares (inflation attack protection)
Breaks when: the share price is manipulated to a level where rounding zeroes out the minted shares
```

For an AMM (constant product):
```
INV-product-nondecreasing
Statement: reserve_x * reserve_y >= k after any swap (k may increase from fees, never decrease)
In plain language: the liquidity pool cannot be drained through swaps
```

For a lending protocol:
```
INV-collateral-solvency
Statement: for all positions, collateral_value >= debt_value * liquidation_threshold
In plain language: the protocol is always overcollateralized across all positions
Breaks when: oracle price drops faster than liquidations can clear, or liquidation incentive is miscalibrated
```

---

## Step 2: Model the economics

Run the numbers manually before running them in code. For each core mechanism, stress test against edge cases:

**Standard stress test questions**:
- What happens at minimum liquidity (e.g. only 1 wei deposited)?
- What happens at maximum values (near uint256 limits)?
- What happens when a single actor holds a dominant position (90%+ of liquidity)?
- What happens when the oracle returns a stale or manipulated price?
- What happens if an external integration (another protocol) behaves unexpectedly?

**Incentive alignment checklist**:
- Who captures fees? In what proportion? Under what conditions?
- What prevents a rational actor from front-running other users?
- Are there circumstances where the protocol's incentives are misaligned with its users?
- What oracle does the protocol depend on, and what happens if that oracle is manipulated?
- Does the protocol create predictable state transitions that can be sandwiched, front-run, or back-run?

**MEV note**: MEV extraction is a design problem, not a deployment afterthought. Commit-reveal schemes, TWAP oracles, and fee structures that redistribute MEV back to LPs are decisions made here, not in Phase 3.

**Use LLM to stress-test math**: Give the model your invariant formulas and edge cases and ask it to try to break them — it's good at algebraic manipulation and often spots division-by-zero scenarios or precision loss. Treat it as a check on your work, not the source of the work.

**What LLMs consistently miss**: Second-order economic effects — behavior that emerges when your protocol composes with others in unexpected ways, or when adversarial actors coordinate across multiple transactions. That reasoning requires a human who understands the broader DeFi landscape.

---

## Step 3: Populate the output files

### INVARIANTS.md

```markdown
# Protocol Invariants

## System Invariants

### INV-[noun]-[property]
**Statement**: [Formal statement]
**Plain language**: [User-facing meaning]
**Breaks when**: [Violation conditions — for fuzz test targeting in Phase 6]
**Phase 6 reference**: [Will become fuzz test target FUZZ-INV-...]

[repeat for each invariant]

## Implicit Invariants
<!-- Invariants that fall out of the design but were never explicitly stated -->
<!-- Do a dedicated LLM pass to surface these before closing Phase 2 -->
```

### ECONOMICS.md

```markdown
# Economic Design

## Core Mechanism
[Description of the fundamental economic mechanism: what the protocol does and how value flows]

## Mathematical Model
[Formulas, invariant expressions, fee calculations]

## Stress Test Results
[Results of edge case analysis — min liquidity, max values, dominant positions, oracle failure]

## Oracle Dependencies
- Oracle: [Chainlink / Uniswap TWAP / custom]
- Staleness threshold: [maxAge in seconds]
- Manipulation resistance: [TWAP window / circuit breaker / other]
- Failure mode: [What the protocol does when oracle is unavailable or returns bad data]

## MEV Considerations
[How the protocol handles sandwich attacks, front-running, back-running]
[Design choices made to mitigate MEV or redistribute it]

## Incentive Alignment
[Fee distribution, governance incentives, user vs protocol alignment analysis]

## Open Questions
[Any economic questions not yet resolved — must close before Phase 3]
→ Mirror to STATE.md
```

---

## Open questions discipline

Every question that cannot be answered with confidence goes into `STATE.md` as an explicit open question. Do not resolve ambiguity silently with an assumption.

An unresolved ambiguity in the economic model — "what happens when X and Y are simultaneously true?" — has a way of becoming an implicit assumption in the code, and implicit assumptions are where edge cases hide. Surface them now, resolve them deliberately, write the decision down.

Every open question from Phase 2 must be explicitly closed before Phase 3 begins.

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- `INVARIANTS.md` contains all invariants with INV-* slugs, including a dedicated pass for implicit invariants
- `ECONOMICS.md` contains the stress test results and the oracle/MEV/incentive analysis
- All open questions from Phase 2 are logged in `STATE.md`
- You can state every invariant the protocol makes to users, formally, without hesitation
- `STATE.md` Phase History updated: `[x] Phase 2 — Economic Design`

Transition: "Phase 2 complete. Say **architecture spec** to begin Phase 3."
