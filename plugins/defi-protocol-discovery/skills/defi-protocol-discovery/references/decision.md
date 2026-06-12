# Phase 6 — Go / No-Go

## Goal

Produce an explicit, reasoned go/no-go decision. If go: produce the Protocol Brief that feeds directly into `defi-spec-driven`.

This phase synthesizes everything — but synthesis is not the first step. Kill criteria come first.

---

## Step 1 — Kill criteria (before synthesis)

Before looking at the accumulated work, establish the conditions under which the answer is NO.

Ask the developer: *"Before we review everything — what would have to be true for you to decide not to build this? Think about the biggest risks we've identified."*

Before asking the developer to generate criteria, review RISKS.md and pre-populate any CRITICAL or HIGH-rated assumptions as candidate kill criteria. Present these as starting points: *"Based on Phase 5, here are suggested kill criteria — confirm each or adjust."* This ensures high-risk assumptions don't slip past the kill criteria step because the developer didn't think to name them.

Before scheduling a validation experiment for any CRITICAL assumption: check whether Phase 4 (ECONOMICS.md) already contains data that evaluates it. If the bear scenario in Phase 4 directly addresses a kill criterion, that criterion's state is determined by that data — TRIGGERED or NOT TRIGGERED. Do not mark it INDETERMINATE if Phase 4 already answered the question.

Confirm 3–5 kill criteria. Number them sequentially (Condition 1, Condition 2, …) and document them in DECISION.md before proceeding to synthesis. This numbering is used for `[TBD pending: condition N]` placeholders in the Protocol Brief — each placeholder refers to its corresponding condition number. Examples:

- "If the validation plan for ASM-lp-demand shows <30% of LPs cite IL as their primary pain"
- "If there's no credible path to $5M sticky TVL after bootstrapping incentives end"
- "If the regulatory surface requires KYC/AML infrastructure we're not willing to build"
- "If the protocol can't survive the RISK-incentive-collapse spiral at current token emission rates"

Once kill criteria are documented, lock them. They cannot be revised during synthesis. This prevents the developer from unconsciously adjusting the bar as they review favorable evidence. Assign final Condition numbers only after the full list is confirmed — do not assign numbers during the confirmation conversation, as reordering or removal shifts all subsequent references. If any `[TBD pending: condition N]` placeholders were written before the list was locked, update them to match the final numbered list before proceeding to Step 2.

---

## Step 2 — Synthesis review

With kill criteria locked, review each phase's output in sequence:

**From PROBLEM.md**: Is the problem real, specific, and worth a protocol? Score: STRONG / ADEQUATE / WEAK
**From LANDSCAPE.md**: Is the competitive position viable? Are early adopters realistic? Score: STRONG / ADEQUATE / WEAK
**From CANVAS.md**: Is the UVP distinct and the mechanism defensible? Score: STRONG / ADEQUATE / WEAK
**From ECONOMICS.md**: Does the protocol survive the bear scenario? Are unit economics viable? Score: STRONG / ADEQUATE / WEAK
**From RISKS.md**: Are the highest-risk assumptions manageable? Are death spirals mitigated? Score: STRONG / ADEQUATE / WEAK

For each WEAK score: is it a blocker, or a known risk to carry forward?

Scoring guidance for the two phases where subjective scoring is most consequential:

**Phase 4 (Economics)**: WEAK if the bear scenario shows protocol failure under market conditions that have occurred at least once in the past 5 years based on historical data. ADEQUATE if the bear scenario shows significant stress but not failure under historical conditions. STRONG if stress-tested against worst-case historical conditions and the protocol demonstrably survives.

**Phase 5 (Risks)**: WEAK if one or more death spirals have HIGH residual risk with no structural mitigation, OR two or more CRITICAL assumptions have no evidence basis and no validation plan. ADEQUATE if all spirals have at least partial mitigation and CRITICAL assumptions have a defined validation path. STRONG if the highest-risk assumptions are validated or carry bounded, explicitly accepted risk.

---

## Step 3 — Kill criteria check

Apply the kill criteria from Step 1 to the synthesis results. Each criterion has one of three states:

- **NOT TRIGGERED**: evidence shows the kill condition is not met → this criterion passes
- **TRIGGERED**: evidence shows the kill condition is met → NO-GO; stop here
- **INDETERMINATE**: the validation experiment has not been run; the criterion cannot be evaluated yet

If any criterion is TRIGGERED: verdict is NO-GO. Document the specific criterion and the evidence. A NO-GO is not a failure — it's the skill working correctly.

If no criterion is TRIGGERED but one or more are INDETERMINATE: do not treat INDETERMINATE as passing. For each INDETERMINATE criterion, classify it immediately as an absolute blocker or stage blocker (classification rule: see Step 5 — apply it here, not just at verdict time). This classification determines how the CONDITIONAL GO conditions are ordered and presented. Proceed to Step 4; each INDETERMINATE criterion becomes a blocking validation condition in the CONDITIONAL GO verdict — not an optional caveat.

If all criteria are NOT TRIGGERED: proceed to Step 4 and expect a GO or CONDITIONAL GO depending on Step 4 results.

---

## Step 4 — Open assumptions review

Review RISKS.md validation plan. For any CRITICAL or HIGH assumption that has NOT been validated:

Present two options to the developer:
1. **Build anyway** — document the assumption as ACCEPTED-AMBIGUITY with risk level and proceed
2. **Validate first** — the go verdict is conditional on validation; don't start spec until the experiment is done

Neither option is wrong. Building with known assumptions is common. The decision must be conscious and documented.

Exception: if an assumption was designated as a kill criterion in Step 1, ACCEPTED-AMBIGUITY is not available for it. Kill-criterion assumptions require either VALIDATE FIRST (producing a CONDITIONAL GO) or, if no validation path exists and the risk is existential, NO-GO. Accepting a kill criterion as an ambiguity would silently nullify the kill criteria established before synthesis.

---

## Step 5 — Verdict

One of three outcomes:

**GO**: The protocol concept is viable enough to commit to specification. All kill criteria pass. High-risk assumptions are either validated or explicitly accepted.

**CONDITIONAL GO**: The protocol concept is viable but conditions must be met before starting spec. Conditions come from two sources: INDETERMINATE kill criteria (Step 3) and VALIDATE FIRST assumptions (Step 4). Type each condition explicitly:

- **Absolute blocker**: if this condition fails → NO-GO regardless of other results. List these first. To determine sequence when absolute blockers come from different domains: order by dependency first (a condition whose resolution is prerequisite for evaluating another comes first), then by time-to-resolution (fastest to validate first, so failure is discovered earliest), then legal/regulatory ahead of economic or market conditions if time-to-resolution is equal. Evaluate in that order — if one fails, stop; don't evaluate the rest. Note on INDETERMINATE absolute blockers: the sequential stop rule applies to TRIGGERED states, not INDETERMINATE ones. Multiple INDETERMINATE absolute blockers may be validated in parallel unless one's validation is logically contingent on another's outcome (e.g., validating market fit before knowing if the product can legally exist is premature). If no logical dependency exists, run validations concurrently.
- **Stage blocker**: if this condition fails → return to the relevant phase and revise before proceeding to spec or build. These are serious but not existential.

Classification rule: a condition is an **absolute blocker** if failure means the protocol cannot legally exist, cannot operate without exposing the team to criminal or civil liability, or requires a fundamental redesign of the core mechanism. A condition is a **stage blocker** if failure means a specific phase's assumptions need revision while the protocol category and general approach remain viable.

The Protocol Brief for a CONDITIONAL GO is produced with placeholders governed by condition type: (a) for fields that depend on an **absolute blocker** condition — use `[TBD pending: condition N]`; the field cannot be meaningfully filled until that condition resolves. (b) for fields that depend on a **stage blocker** condition — write the current best value and append `[subject to revision if condition N fails]`; the field is known but may change. Do not withhold the entire Protocol Brief for stage blocker conditions alone — withhold only for unresolved absolute blockers. Do not project unvalidated numbers from absolute-blocker conditions into the handoff document — the spec team should know what's real and what's assumed.

**NO-GO**: One or more kill criteria triggered. Document the specific reason. Then identify what would have to change for a future re-evaluation — specifically: (1) which kill criterion was triggered, (2) what mechanism change or external condition would remove it, and (3) at which phase re-evaluation would begin. Re-entry phase rule: Phase 3 (DeFi Lean Canvas) is the re-entry for mechanism design problems — when the core how of the protocol needs to change. Phase 4 (Economic Viability) is the re-entry for economic model problems — when the mechanism is sound but fee levels, TVL assumptions, or bootstrap cost need revision. If multiple criteria triggered and point to different re-entry phases, state all of them and identify the earliest as the primary re-entry point (later phases will need to be re-derived once the earlier phase changes). This guidance is required when the NO-GO is triggered by a mechanism design or economic problem. It is optional only when triggered by an external condition outside the developer's control (regulatory ban, market condition).

---

## Protocol Brief (Go and Conditional Go only)

The Protocol Brief is the handoff document to `defi-spec-driven`. It must be precise enough that the init phase of the spec process can start without re-deriving context.

```markdown
# Protocol Brief

## Identity
Working name: [name]
Category: [AMM / lending / yield vault / derivatives / infrastructure / other]
Target network: [or TBD if not yet decided]

## Problem
[PROBLEM.md problem statement, one paragraph]

## Target Segment
Primary: [from CANVAS.md]
Early adopters: [specific — from LANDSCAPE.md]

## Regulatory Status
[CLEAR / REQUIRES CONSULTATION / TBD pending legal opinion on (specific question)]
[One sentence on regulatory surface and required action before launch, if any]
Value rules: CLEAR if Phase 5 found no regulatory surface. REQUIRES CONSULTATION if Phase 5 identified surface (stablecoins, discretionary allocation, securities-adjacent, US access) but no legal opinion obtained. TBD pending legal opinion on (specific question) if consultation is engaged but opinion not yet received — use the exact question from RISKS.md Regulatory Status line.

## Core Value Proposition
[From CANVAS.md UVP field — one sentence]

## Unique Mechanism
[From CANVAS.md — the how]

## Economic Model
Revenue source: [from ECONOMICS.md — sustainable streams only]
Fee structure: [specific]
Bootstrap path: [mechanism + cost + timeline]
Break-even TVL: [from ECONOMICS.md]

## Open Assumptions (carry forward into spec)
| Slug | Assumption | Risk | Status |
|---|---|---|---|
| ASM-X | [assumption] | HIGH | ACCEPTED-AMBIGUITY — [rationale] |
| ASM-Y | [assumption] | HIGH | VALIDATE FIRST — [experiment and deadline] |

Population rules: (1) Include ACCEPTED-AMBIGUITY assumptions — known risks the spec team must design around. (2) Include VALIDATE FIRST assumptions — unresolved conditions the spec team must not treat as confirmed. (3) Exclude VALIDATED assumptions — they are resolved. (4) Exclude LOW-risk assumptions regardless of status.

## Handoff Note
Start defi-spec-driven with: "[one sentence framing for the init prompt]"
Example: "A fixed-rate lending protocol for protocol treasuries on Ethereum mainnet, greenfield, targeting $10M TVL in 12 months."
```

---

## DECISION.md Template

```markdown
# Go / No-Go Decision

## Kill Criteria (defined before synthesis)
Condition 1: [Criterion 1]
Condition 2: [Criterion 2]
Condition 3: [Criterion 3]
...

## Synthesis Review
| Phase | Output | Score | Notes |
|---|---|---|---|
| Phase 1 — Problem | PROBLEM.md | STRONG / ADEQUATE / WEAK | [note] |
| Phase 2 — Landscape | LANDSCAPE.md | STRONG / ADEQUATE / WEAK | [note] |
| Phase 3 — Canvas | CANVAS.md | STRONG / ADEQUATE / WEAK | [note] |
| Phase 4 — Economics | ECONOMICS.md | STRONG / ADEQUATE / WEAK | [note] |
| Phase 5 — Risks | RISKS.md | STRONG / ADEQUATE / WEAK | [note] |

## Kill Criteria Check
Condition # corresponds to the numbered list in Kill Criteria above.

| Condition # | Criterion | Classification | State | Evidence |
|---|---|---|---|---|
| 1 | [criterion 1] | Absolute blocker / Stage blocker | NOT TRIGGERED / TRIGGERED / INDETERMINATE | [evidence or "validation not run"] |
| 2 | [criterion 2] | Absolute blocker / Stage blocker | NOT TRIGGERED / TRIGGERED / INDETERMINATE | [evidence or "validation not run"] |

## Unvalidated High-Risk Assumptions
| Slug | Decision | Rationale |
|---|---|---|
| ASM-X | ACCEPTED-AMBIGUITY | [why acceptable to proceed] |
| ASM-Y | VALIDATE FIRST | [experiment, deadline] |

## Verdict
**[GO / CONDITIONAL GO / NO-GO]**

[One paragraph rationale]

---

[Protocol Brief — if GO or CONDITIONAL GO]
```
