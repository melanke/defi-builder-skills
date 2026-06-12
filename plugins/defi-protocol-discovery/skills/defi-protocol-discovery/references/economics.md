# Phase 4 — Economic Viability

## Goal

Stress-test the economic model. By the end of this phase, the developer should be able to answer: is this protocol financially viable at realistic TVL, and is the yield sustainable?

The most common failure mode in DeFi is not a hack — it's an economic model that works in a bull market and collapses when incentives dry up. This phase finds that before it's built.

---

## Step 1 — Sustainable yield audit

The first question, before any projections: *"Where does the yield come from, exactly?"*

Classify every yield source in the protocol:

| Yield source | Type | Sustainable? |
|---|---|---|
| Trading fees | Real (paid by users) | Yes |
| Interest spread | Real (paid by borrowers) | Yes |
| Token emissions | Inflationary | Only if emissions decrease over time to a real-yield floor |
| Treasury subsidies | Finite | No — define the runway |
| External protocol yield (e.g., Aave, Compound) | Dependent | Yes, but carries dependency risk |
| MEV/arbitrage profits | Real but variable | Partially — can't be projected reliably |

For each inflationary or finite source: what happens to user behavior when it ends? If the answer is "they leave", the protocol has a bootstrapping strategy but not a sustainability plan.

**Required output**: a yield sustainability statement — one paragraph that honestly characterizes whether the protocol has real yield, inflationary yield, or a transition plan from one to the other.

---

## Step 1.5 — Primary revenue variable diagnostic

Before modeling scenarios, identify the variable that most directly drives protocol revenue.

| Type | Revenue driver | Scenario axis |
|---|---|---|
| **TVL-driven** | fee rate × TVL (deposit fee, management fee) | Model bear/base/bull on TVL axis |
| **Volume-driven** | fee rate × volume (swap fee, liquidation fee) | Model bear/base/bull on volume axis; volume ≠ TVL |
| **Rate-driven** | spread × TVL, where spread moves with market rates (lending spread, fixed-rate protocol) | Model bear/base/bull on rate axis, not TVL axis |

Most protocols have a mix, but one variable dominates. Identify it before building the Step 3 scenarios — it determines which axis matters.

**Rate-driven protocols require explicit attention**: in a bear market, rates compress faster and harder than TVL drops. A protocol with $50M TVL at 8% spread generates the same revenue as one with $100M TVL at 4% spread — and bear markets typically compress both simultaneously. Modeling only TVL scenarios for a rate-driven protocol produces a misleadingly comfortable bear case.

Diagnostic: calculate revenue at three rate levels (current rate, 50% compressed, near-zero) at constant TVL. If the difference across rate scenarios exceeds the difference across TVL scenarios, the protocol is rate-driven and Step 3 must vary rates independently.

Record the classification and diagnostic basis in the Primary Revenue Variable section of ECONOMICS.md before proceeding to Step 2.

---

## Step 2 — Revenue model

For each revenue stream identified in the canvas:

1. **Fee structure**: what's the exact fee? (e.g., 0.3% of volume, 10% performance fee on yield, $X/year for a subscription tier)
2. **Who bears it**: LP, trader, depositor, borrower — and is this fee competitive with alternatives?
3. **Protocol capture**: what percentage goes to the protocol vs LPs vs token holders?
4. **Volume/TVL dependency**: at what TVL or volume does this fee generate meaningful revenue?

Calculate breakeven: *"At what TVL/volume does protocol fee revenue cover protocol operating costs (oracles, keepers, audits)?"* This is the minimum viable scale.

If Phase 3 produced a provisional break-even TVL estimate (in CANVAS.md or as an OQ), compare it to this result. If they agree within 20%, close the Phase 3 OQ. If they differ by more than 20%, log the discrepancy and its cause in STATE.md, update CANVAS.md's Liquidity Bootstrapping field with the Phase 4 figure, and note that the Phase 4 number is authoritative. The Phase 3 figure is a pre-check; Phase 4 is the binding calculation.

---

## Step 3 — TVL scenario analysis

Before building scenarios: check STATE.md for the note "Phase 3 should apply the bifurcated v1/v2 pattern" and any canvas confirmation that v1 → v2 requires user re-acquisition. If re-acquisition is confirmed, run the TVL scenario table twice — once for v1 and once for v2 as independent bootstrapping events. Do not aggregate v1 and v2 TVL; v2 bootstrapping starts from zero when re-acquisition is required. Label the two tables clearly. If the v1/v2 note is absent, proceed with a single table.

Build three TVL scenarios: bear, base, and bull. For each scenario, project:

- TVL at 6 months, 12 months, 24 months
- Volume (if applicable) as a multiple of TVL
- Protocol revenue (from revenue model above)
- Protocol costs
- Net position (revenue minus costs)

**If Step 1.5 identified this as rate-driven**: restructure the scenario axis. Instead of three TVL scenarios, run a 2×3 matrix: two TVL levels (bootstrapping floor and target TVL) × three rate scenarios (current rate, 50% compressed, near-zero). The bear case for a rate-driven protocol must compress both rate and TVL simultaneously — a bear case where only TVL drops while rates hold constant produces a misleadingly comfortable result.

**Bear case**: TVL stagnates at bootstrapping floor. Incentives run out. What's the protocol's state?
**Base case**: TVL grows to the target segment's realistic capture (use on-chain data from Phase 2 comps).
**Bull case**: TVL grows to best-case based on analogues in Phase 2.

Anchor all projections in real data where possible. Competitor TVL from Phase 2 is the calibration point.

The scenario analysis is not a forecast — it's a stress test. The question is: does the protocol survive the bear case? If not, what kills it, and is that kill condition avoidable?

---

## Step 4 — Unit economics

Calculate protocol revenue per $1M of TVL (or per $1M of volume, depending on the model).

This single number is the most useful economic benchmark in DeFi. It allows:
- Direct comparison to competitors (who are they more efficient than?)
- Minimum scale calculation (TVL needed to be self-sustaining)
- Sensitivity analysis (what happens if fee rates compress by 50%?)

For the current protocol:
- Revenue per $1M TVL at current fee structure: `$X`
- Revenue per $1M TVL at compressed fees (50% lower): `$Y`
- Breakeven TVL at current fees: `$Z`
- Breakeven TVL at compressed fees: `$W`

---

## Step 5 — Liquidity bootstrapping cost

From the canvas, the bootstrapping mechanism was identified. Now price it.

For each bootstrapping mechanism, estimate:
- Total cost to reach minimum viable TVL
- Duration of incentive spend
- Expected decay in TVL when incentives end (based on analogues)
- Residual "sticky" TVL after decay

If the sticky TVL after decay is above the breakeven point from Step 2: the bootstrapping strategy is viable.
If the sticky TVL after decay is below breakeven: the protocol enters a death spiral — costs exceed revenue, requiring more incentives, which the treasury can't sustain. Log this as `RISK-incentive-collapse-liquidity` in Phase 5.

---

## Step 6 — Token role (if applicable)

If the protocol has or plans a governance token, answer explicitly:

1. **What does the token do?** (governance only / fee sharing / staking / veTokenomics / other)
2. **Does the token create protocol value or extract it?** (a token that receives all fees extracts value from LPs; a token that boosts yields for stakers without revenue backing inflates without creating)
3. **Emission schedule**: total supply, unlock schedule, initial distribution. What's the dilution rate in year 1?
4. **Circular dependency check**: does the protocol's economic model depend on its own token price? (If yes, this is a red flag — log as `ASM-token-price-stable` in Phase 5)

If no token: note whether a token is planned and when, or confirm it's intentionally tokenless. A tokenless protocol must have a different sustainability model — document it.

---

## ECONOMICS.md Template

```markdown
# Economic Viability

## Sustainable Yield Audit
| Yield source | Type | Sustainable? | Notes |
|---|---|---|---|

**Yield sustainability statement**: [one honest paragraph]

## Primary Revenue Variable
- Classification: [TVL-driven / volume-driven / rate-driven]
- Dominant driver: [fee rate × TVL / fee rate × volume / spread × TVL]
- Diagnostic basis: [e.g., "rate scenario spread = $X, TVL scenario spread = $Y → rate-driven"]
- Scenario axis used in Step 3: [TVL axis / volume axis / 2×3 rate×TVL matrix]

## Revenue Model
| Stream | Fee structure | Protocol capture | Breakeven TVL/volume |
|---|---|---|---|

## TVL Scenarios
<!-- If v1/v2 re-acquisition confirmed in STATE.md: duplicate this table for v1 and v2 as separate events -->
| Scenario | 6mo TVL | 12mo TVL | 24mo TVL | Revenue | Costs | Net |
|---|---|---|---|---|---|---|
| Bear | | | | | | |
| Base | | | | | | |
| Bull | | | | | | |

**Bear case survival**:
- Verdict: [SURVIVES / DOES NOT SURVIVE]
- Kill condition (if applicable): [what ends the protocol — e.g., treasury exhausted at month N when TVL < $X]
- Kill condition status: [ADDRESSED (describe fix) / ACCEPTED-RISK (log as RISK-* for Phase 5)]

## Unit Economics
- Revenue per $1M TVL (current fees): $[X]
- Revenue per $1M TVL (fees compressed 50%): $[Y]
- Breakeven TVL (current): $[Z]
- Breakeven TVL (compressed): $[W]

## Bootstrapping Cost
- Mechanism: [from canvas]
- Total cost to min viable TVL: $[X]
- Duration: [weeks/months]
- Expected sticky TVL post-decay: $[Y]
- Viable: [yes / no / conditional on...]

## Token Role (if applicable)
- Function: [governance / fee sharing / staking / other]
- Value creation vs extraction: [assessment]
- Circular dependency: [yes / no — if yes, flagged as ASM-*]
- Emission schedule summary: [total supply, year 1 dilution %]

## Open Questions
[Economic uncertainties logged as OQs from this phase]
```

---

## Advancing to Phase 5

Gate check:

1. Is every yield source classified as real, inflationary, or finite — and is there a sustainability path? If not, stay.
2. Does the protocol survive the bear scenario? If not, stay until the kill condition is addressed or recorded as ACCEPTED-RISK with a `RISK-*` slug logged in STATE.md for Phase 5.
3. Is the bootstrapping cost realistic against available resources? If not, stay.
4. Is any circular token dependency identified and logged? If yes, ensure it's queued for Phase 5.

When satisfied: *"Economic viability closed. Moving to Phase 5 — Risk & Assumptions, where we rank what we've assumed and map what could kill the protocol."*
