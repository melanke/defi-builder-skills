# Phase 5 — Risk & Assumptions

## Goal

Make every assumption from Phases 1–4 explicit, rank them by risk, map the scenarios that could kill the protocol, and produce a validation plan for the most dangerous assumptions.

This phase does not produce new ideas. It produces clarity about what we don't know, and what we need to know before committing to build.

---

## Step 1 — Assumption extraction

Every decision made in Phases 1–4 rested on assumptions. Extract them all. Pull from:

- `PROBLEM.md`: assumptions about the problem being real and the segment caring
- `LANDSCAPE.md`: assumptions about competitive dynamics and early adopter behavior
- `CANVAS.md`: assumptions about the UVP, mechanism, channels, revenue
- `ECONOMICS.md`: assumptions about TVL growth, fee sustainability, bootstrap stickiness

Also pull from STATE.md: any OQs that were converted to ACCEPTED-AMBIGUITY carry implicit assumptions that belong here.

For each assumption, assign:
- `ASM-{domain}-{claim}` slug
- **Risk level**: LOW / MEDIUM / HIGH / CRITICAL
- **Basis**: what evidence or reasoning supports this assumption being true?
- **Invalidation condition**: what would have to be true for this assumption to be wrong?

Risk assessment formula:
- **Impact if wrong**: low (annoying) / medium (requires pivot) / high (protocol doesn't work) / critical (protocol fails or gets drained)
- **Probability of being wrong**: low (well-evidenced) / medium (educated guess) / high (thin evidence) / unknown

Risk level = max(impact, probability) with weight toward impact.

---

## Step 2 — Death spiral mapping

A death spiral is a self-reinforcing failure sequence specific to the protocol's design. Map 2–4 plausible death spirals.

For each death spiral, explicitly address:
- *Does this spiral have a natural floor in the current design?* A spiral that self-limits has different severity than one without a floor. If no floor exists, state it — it changes the residual risk rating.
- *Is this a parameter problem or a structural problem?* A parameter problem (wrong ratio, too-high fee) can be fixed without redesigning the mechanism. A structural problem (circular dependency, reflexive collateral) requires the mechanism to work differently. Flag structural problems at HIGH residual risk regardless of any proposed mitigations.
- *Does this spiral structurally resemble a known historical DeFi failure?* If yes, name it. A named historical precedent is direct evidence the spiral is viable, not theoretical — it is the strongest argument for raising the risk rating.

For each death spiral:
- `RISK-{trigger}-{consequence}` slug
- Trigger condition: what initiates the spiral
- Mechanism: what self-reinforces (why it doesn't stop on its own)
- Terminal state: what the protocol looks like at the end of the spiral
- Current mitigation: what in the design prevents or limits this (if anything)
- Residual risk: if mitigations exist, how much risk remains?

**Common DeFi death spirals to check against your specific design:**

| Spiral type | Trigger | Self-reinforcing mechanism |
|---|---|---|
| Bank run | Large withdrawal event | Liquidity thins → slippage rises → more withdrawals |
| Depeg | Peg loses confidence | Redemptions drain collateral → collateral drops → more redemptions |
| Liquidation cascade | Price drop exceeds collateral buffer | Forced sales depress price → more positions undercollateralized |
| Oracle manipulation | Low liquidity oracle feed | Price manipulated → protocol drained before correction |
| Incentive collapse | Emissions end or token price falls | LPs leave → TVL drops below useful threshold → more LPs leave |
| Governance attack | Token concentration | Hostile governance vote → protocol drained or parameters changed adversarially |
| Circular collateral | Protocol's own token used as collateral | Token price falls → collateral value falls → liquidations depress token further |

Not all spirals are fatal. Some have natural floors (liquidations stop when collateral ratio improves). Document the natural floor if one exists.

**Coupling pass (required after all spirals are individually mapped)**: For each pair of spirals, ask — does the terminal state of spiral A create or worsen the trigger condition of spiral B? If yes, mark both as coupled (each references the other's slug in the "Coupled with" field) and write a combined terminal state describing the protocol when both spirals run concurrently. If a spiral has no coupling, write "none." This pass is required even when only two spirals are mapped.

---

## Step 3 — Regulatory and dependency risk

**Regulatory surface**: does this protocol touch any of the following?
- Stablecoins (especially algorithmic or yield-bearing) — high regulatory scrutiny
- **Third-party discretionary allocation**: if any team member, curator, or strategist makes decisions about where depositor funds are allocated — even partially — this may qualify as investment adviser activity under the US Investment Advisers Act 1940 or equivalent. Applies to: yield vaults with human-curated strategies, managed accounts, any protocol where team discretion determines user yield beyond pure smart contract automation. If checked: legal consultation is required before launch, not after.
- KYC/AML requirements — particularly for protocols targeting institutional users
- Securities-adjacent instruments (yield products that may be classified as securities)
- US user access — if the protocol is accessible from the US, OFAC and FinCEN apply

Flag the regulatory surface explicitly. This is not a legal opinion — it's a signal to get one before launch. If the discretionary allocation box is checked, prioritize that legal consultation above all other validation experiments — it's cheap ($5–15k), fast (4 weeks), and the failure mode is existential.

At the end of this step, write a single-line regulatory status summary in RISKS.md under the Regulatory Surface section — this line populates the Protocol Brief Regulatory Status field in Phase 6:

**Regulatory Status**: [CLEAR | REQUIRES CONSULTATION | TBD pending legal opinion on (specific question)]

Value rules: CLEAR if none of the above boxes apply. REQUIRES CONSULTATION if one or more boxes apply and no legal opinion has been obtained. TBD pending legal opinion on [specific question] if legal consultation has been engaged but opinion not yet received — use the exact question from above (e.g., "Can this protocol operate without registering as an investment adviser under the IAA 1940?").

When logging this in the assumption register, use slug `ASM-regulatory-investment-adviser` at risk level CRITICAL. Without a slug, this flag will not carry forward to the Phase 6 kill criteria table.

The legal consultation should answer specifically: *"Can this protocol operate as described without registering as an investment adviser under the Investment Advisers Act 1940 (or local equivalent), and if yes, under which exemption?"* A non-answer from counsel does not constitute validation.

Note: if registration is required and not acceptable, a real alternative is redesigning the allocation mechanism to be fully algorithmic — removing human discretion entirely. This is a significant product architecture decision with UX tradeoffs, but it is an option that should be evaluated before defaulting to "abandon the concept."

**External dependencies**: the protocol's assumptions include assumptions about the systems it depends on:
- Oracle providers (uptime, manipulation resistance, feed availability)
- External protocols (Aave, Uniswap, Chainlink — their contract risk becomes your contract risk)
- Bridges (if cross-chain — bridge failures are one of DeFi's most common catastrophic risks)
- Governance of dependencies (a protocol that owns your liquidity can change rules)

For each critical dependency, log an `ASM-*` entry covering its reliability assumption.

---

## Step 4 — Validation plan

For the top 3–5 highest-risk assumptions: identify the cheapest experiment that would confirm or refute them before significant engineering investment.

Validation approaches in DeFi:
- **On-chain data query**: if the assumption is about existing user behavior, the answer may already be in on-chain data (Dune, Flipside, DefiLlama)
- **User interviews**: if the assumption is about willingness to use a protocol, 5–10 conversations with the target segment can validate or invalidate
- **Competitor analysis**: if a competitor exists, their TVL/volume/retention data is indirect validation
- **Simulation / backtesting**: if the assumption is about mechanism behavior or historical performance, a simulation or backtest can stress-test it before code
- **Legal consultation**: for regulatory and compliance assumptions, a legal opinion from DeFi-specialized counsel is the only valid validation. On-chain data and user interviews cannot validate legal risk. Get the opinion before committing to build, not after.
- **Governance proposal**: if the early adopter is a DAO/treasury, a governance proposal for a pilot integration is a real validation

For each validation:
- What's the experiment?
- What result would confirm the assumption?
- What result would refute it?
- Cost and time estimate?

---

## RISKS.md Template

```markdown
# Risk & Assumptions

## Assumption Register
| Slug | Assumption | Risk level | Basis | Invalidation condition | Status |
|---|---|---|---|---|---|
| ASM-lp-demand | LPs will move from Uniswap V3 for better IL protection | HIGH | Anecdotal; no quantified demand | Survey of top LPs shows <10% would switch | NOT VALIDATED |
| ASM-fee-sufficient | 0.05% fee generates enough revenue at target TVL | MEDIUM | Unit economics calc in ECONOMICS.md | Competitor fee compression below 0.03% | NOT VALIDATED |
| ... | | | | | |

Status rules:
- **NOT VALIDATED**: assumption extracted but no validation work started
- **IN PROGRESS**: a validation experiment has been initiated (interviews scheduled, legal counsel engaged) but not yet concluded
- **VALIDATED**: experiment completed and assumption confirmed or refuted with evidence
- **ACCEPTED-AMBIGUITY**: acknowledged as unresolvable before launch; risk consciously accepted

Phase 6 note: when surfacing CRITICAL/HIGH assumptions as candidate kill criteria, skip any with Status VALIDATED — they have already been evaluated.

## Death Spirals
### RISK-[trigger]-[consequence]
**Trigger**: [condition]
**Mechanism**: [self-reinforcing loop]
**Terminal state**: [end state]
**Mitigation**: [what's in the design to prevent/limit this]
**Residual risk**: [LOW / MEDIUM / HIGH]
**Coupled with**: [RISK-slug if this spiral feeds into or amplifies another; "none" if independent]
**Combined terminal state**: [if coupled: what does the terminal state look like when both spirals run simultaneously?]

### RISK-...
...

## Regulatory Surface
[Checklist of regulatory exposure areas, if any]
[Note: get legal opinion before launch if any boxes are checked]

## External Dependencies
| Dependency | What we assume | Failure mode | Mitigation |
|---|---|---|---|
| [Oracle: Chainlink] | [99.9% uptime, manipulation resistant] | [stale price → bad liquidations] | [circuit breaker, backup oracle] |
| ... | | | |

## Validation Plan
| Assumption | Experiment | Confirms if | Refutes if | Cost | Timeline |
|---|---|---|---|---|---|
| ASM-lp-demand | Interview 10 top Uniswap V3 LPs | >6/10 cite IL as primary pain | <3/10 would switch for IL solution | ~20h | 2 weeks |
| ... | | | | | |

## Overall Risk Assessment
[One paragraph: the 2–3 highest-risk assumptions and what the validation plan should prioritize before committing to build]
```

---

## Advancing to Phase 6

Gate check:

1. Are all major assumptions from Phases 1–4 extracted and slugged? If STATE.md has ACCEPTED-AMBIGUITY entries, are they reflected here?
2. Are 2–4 death spirals mapped? If only one exists, that's allowed — but justify it.
3. Is a validation plan defined for the top 3 highest-risk assumptions?
4. Are all OQs from this phase resolved or converted to ACCEPTED-AMBIGUITY?

5. For any CRITICAL or HIGH assumption marked ACCEPTED-AMBIGUITY: flag to the developer that if Phase 6 designates this assumption as a kill criterion, its ACCEPTED-AMBIGUITY status will need to reclassify to VALIDATE FIRST or NO-GO. Surface this before advancing: *"[ASM-slug] is currently ACCEPTED-AMBIGUITY. If Phase 6 elevates it to a kill criterion, that status will need to change to VALIDATE FIRST. Consider resolving it before advancing, or carry it forward knowingly."* This warning does not block advancement. If the developer advances with the status unchanged, log their explicit acknowledgment in STATE.md under the relevant assumption entry — Phase 6 will re-surface this as a status conflict that must be resolved there.

When satisfied: *"Risk & Assumptions closed. Moving to Phase 6 — Go/No-Go, where we synthesize and decide."*
