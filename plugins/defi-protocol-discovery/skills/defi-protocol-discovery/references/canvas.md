# Phase 3 — DeFi Lean Canvas

## Goal

Build a complete canvas that captures the protocol's value proposition, unique mechanism, and business model in one document. By the end of this phase, every major dimension of "why this protocol, for whom, and how it sustains itself" should be answered or explicitly flagged as open.

The canvas is not the end — it's the test. Every field that can't be filled confidently is an open question that will cost you later.

---

## DeFi Lean Canvas fields

The standard Lean Canvas has been adapted for DeFi. Eleven fields, in the order they should be filled (dependency order, not display order):

### 1. Problem
*What specific problem does this protocol solve?*

Pull directly from `PROBLEM.md`. If it has changed since Phase 1, log it as a pivot in STATE.md.

### 2. Customer Segments
*Who has this problem, and who will adopt first?*

Two sub-fields:
- **Primary segment**: the long-term target user
- **Early adopters**: the specific people/protocols who will use a v1 with smart contract risk and no track record

Pull early adopters from `LANDSCAPE.md`. If the primary segment and early adopters differ significantly, document that gap — early adopter fit drives bootstrapping, not TAM.

### 3. Unique Value Proposition (UVP)
*Why should the early adopter use this over what exists today?*

One sentence, specific and falsifiable. "Better yields" is not a UVP. "Fixed-rate lending without protocol-side counterparty risk, using isolated vault architecture" is a UVP.

The UVP must be grounded in the differentiation map from Phase 2. If the protocol isn't positioned distinctly on at least two dimensions, the UVP is weak.

### 4. Unique Mechanism
*How does the protocol deliver the UVP in a way that's hard to replicate?*

This is distinct from the UVP (what) — it's the how. In DeFi, the mechanism is often the moat. Ask: *"If someone deployed a fork of this tomorrow, what would they be missing?"*

Valid answers: proprietary algorithm, protocol-owned liquidity strategy, governance model, first-mover liquidity network effects, novel mathematical primitive, specific integration dependencies.

Weak answers: "we'll have a better UX", "we'll deploy faster", "our team is better".

### 5. Channels
*How do users find and access the protocol?*

DeFi channels are different from traditional software:
- **Aggregators**: 1inch, Paraswap, DeFiLlama, Zapper — the protocol needs to be indexable
- **Integrations**: other protocols using this one as infrastructure (yield strategies, routers, vaults)
- **Frontends**: does the protocol need a UI, or is it purely composable infrastructure?
- **Community**: Discord/Twitter presence, DAO governance participation
- **Direct protocol relationships**: if early adopters are protocols, the channel is BD, not marketing

Note: if "user is a protocol", the channel is integration docs and SDKs — not a consumer-facing frontend.

### 6. Revenue Streams
*Where does protocol revenue come from?*

Be precise. For each stream:
- Source (who pays, for what)
- Structure (flat fee, % of volume, spread, performance fee, etc.)
- Sustainability (is this real revenue or token inflation?)

**Required question**: *"Would this revenue exist if the protocol had no governance token and no inflation?"* If the answer is no, the protocol currently has no sustainable revenue — only incentivized activity. Document this explicitly; it's not disqualifying, but it requires a path to real revenue.

### 7. Cost Structure
*What does it cost to run this protocol sustainably?*

For DeFi protocols, include:
- Oracle costs (Chainlink, Pyth, or custom — not free)
- Keeper/liquidator incentives (if the protocol requires external agents)
- Audit costs (recurring, not one-time)
- Multisig/governance operational overhead
- Liquidity mining budget (if any — and for how long)

A protocol with no token and no revenue is self-sustaining only if its costs are zero. They never are. Map the costs.

### 8. Key Metrics
*What numbers tell you whether the protocol is working?*

Pick 3–5 metrics that directly measure protocol health — not vanity metrics. For each, state the threshold that would signal success or failure.

Common DeFi metrics by protocol type:
- **Lending**: utilization rate, bad debt ratio, total supplied
- **AMM/DEX**: volume, LP returns vs IL, pool depth
- **Yield vault**: APY sustainability score (real yield / emissions ratio), TVL growth
- **Derivatives**: open interest, liquidation efficiency, funding rate balance

Avoid: "total users", "Discord members", "TVL" in isolation (TVL without revenue context is a vanity metric).

### 9. Unfair Advantage
*What does this protocol have that's genuinely hard to copy?*

Be honest. Most protocols don't have a strong unfair advantage at launch. If that's true here, say so — and identify what could become one over time (liquidity network effects, governance consolidation, first-mover brand in a niche).

Valid: deep integration with a specific protocol ecosystem, proprietary oracle infrastructure, team with exclusive relationship to key liquidity providers, novel math that's hard to replicate without the insight.

Not valid: "we'll move faster", "our community will be loyal".

### 10. Liquidity Bootstrapping
*How does the protocol acquire its first meaningful TVL?*

This field doesn't exist in the standard Lean Canvas. In DeFi it's non-optional.

For most protocols, liquidity begets liquidity — the protocol is useless below a minimum TVL threshold. The bootstrapping strategy must get above that threshold before incentives run out.

Map: what's the minimum viable TVL for the protocol to be useful? What's the bootstrapping mechanism (emissions, POL, partnerships, grants, seed LPs)? What's the total cost? What's the timeline?

**Break-even TVL (attempt now; log as OQ blocking Phase 4 if inputs are not yet confirmed):**

```
Break-even TVL = annual operating costs ÷ annual revenue per $1M TVL
```

If break-even TVL is above the target segment's realistic addressable pool, that is a viability flag — not a note to bury in Phase 4. Surface it here, while the canvas is open. If the number can't be calculated yet (revenue model is incomplete), that means the bootstrapping field can't be honestly filled — log it as an OQ blocking Phase 4.

If seed TVL is below break-even, document the capital bridge explicitly: how long does the team fund the deficit, and what's the mechanism that closes the gap (grant, investor, token raise, organic growth)?

### 11. Exit / Sustainability Plan
*What does this protocol look like in 3 years if it succeeds?*

One short paragraph. Forces clarity on: does this need a token? Is it a public good or a revenue-generating protocol? What does "winning" mean in this space?

---

## Bifurcated v1/v2 pattern

Some protocol mechanisms depend on ecosystem preconditions that don't yet exist at full adoption — Uniswap V4 hooks at meaningful liquidity depth, EigenLayer restaking at scale, account abstraction wallet penetration above a useful threshold.

Check STATE.md before filling the canvas. If STATE.md contains the note *"Phase 3 should apply the bifurcated v1/v2 pattern"* — whether it was added in Phase 1 or first added at the landscape gate — the canvas cannot be filled as a single product; apply the split below. If that note is absent (including if it was present in Phase 1 but cleared at the landscape gate because the precondition is already met), skip this section entirely.

Apply the v1/v2 split explicitly when this applies:

**v1 (works today)**: what can the protocol deliver using the existing ecosystem? This version must be independently useful — not a stripped-down placeholder. If v1 has no standalone value proposition, the bootstrapping strategy depends entirely on the precondition arriving on schedule. That is a bet, not a plan.

**v2 (unlocks the differentiating mechanism)**: when the precondition is met, what does the protocol become? The v2 UVP is the full vision; v1 is what earns the user base and liquidity that v2 inherits.

Fill the UVP, Unique Mechanism, and Revenue Streams canvas fields twice — once for v1, once for v2. If they come out identical, the precondition isn't actually load-bearing and the split isn't needed.

**Required questions when applying this pattern**:
1. *Does v1 generate enough value to bootstrap liquidity independently of v2's mechanism?* If no, surface the precondition timeline as a viability question, not a background assumption.
2. *What's the realistic adoption timeline for the precondition?* If this can't be estimated from on-chain data or protocol roadmaps, log as an OQ.
3. *Is the v1 → v2 transition a migration the user will make, or will they need to be re-acquired?* If re-acquisition is required, v1 TVL does not transfer — model v1 and v2 as separate bootstrapping events and price each separately. When re-acquisition is confirmed, the Liquidity Bootstrapping canvas field must contain estimates for both v1 and v2 as separate events. A v2 estimate can be order-of-magnitude at this stage — what matters is that it is present. A canvas showing only v1 bootstrapping when re-acquisition is confirmed produces a systematically underestimated capital requirement, and the Phase 3 gate cannot confirm a credible bootstrapping strategy. When re-acquisition is confirmed, also log to STATE.md: `v1→v2 re-acquisition required` — Phase 4 (economics.md) reads this flag to determine whether to run separate TVL scenario tables for v1 and v2.

If the protocol has no precondition dependency, skip this section entirely.

---

## CANVAS.md Template

```markdown
# DeFi Lean Canvas

## Problem
[From PROBLEM.md — one paragraph]

## Customer Segments
**Primary segment**: [description]
**Early adopters**: [specific — from LANDSCAPE.md]

## Unique Value Proposition
[One sentence, specific and falsifiable]

## Unique Mechanism
[What makes this hard to fork effectively]

## Channels
[How users/protocols find and access this]

## Revenue Streams
| Stream | Who pays | Structure | Sustainable without token? |
|---|---|---|---|
| [name] | [who] | [structure] | [yes / no / partial] |

## Cost Structure
| Cost | Type | Est. magnitude | Notes |
|---|---|---|---|
| [oracle] | recurring | [range] | [provider] |
| [keepers] | variable | [range] | [trigger] |
| [audits] | recurring | [range] | [cadence] |

## Key Metrics
| Metric | Why it matters | Success threshold | Failure threshold |
|---|---|---|---|
| [metric] | [reason] | [number] | [number] |

## Unfair Advantage
[Honest assessment — what's real vs aspirational]

## Liquidity Bootstrapping
Minimum viable TVL: [$]
Mechanism: [how]
Estimated cost: [$]
Timeline: [weeks/months]

## Sustainability Plan
[One paragraph on long-term sustainability]

## Open Questions
[Any unresolved canvas fields, logged from this phase]
```

---

## Advancing to Phase 4

Gate check:

1. Are all 11 fields filled — or explicitly flagged as open questions? If a field can't be filled, that's allowed, but it must be an OQ, not a blank.
2. Is there at least one sustainable revenue stream (independent of token emissions)? If not, log as OQ and note the risk.
3. Is the UVP distinct on at least two dimensions from the differentiation map? If not, stay.
4. Is the bootstrapping strategy credible (not "we'll figure it out later")? Required sub-checks:
   (a) Is the minimum viable TVL estimated and filled in the canvas? If not, stay.
   (b) If the mechanism is token emissions only: is there at least an order-of-magnitude estimate of TVL decay when emissions end, based on a Phase 2 analogue? If no analogue was identified and no decay estimate exists, flag as OQ blocking Phase 4 and stay. A plan that names token emissions but does not address post-emissions TVL retention defers the hardest question to Phase 4 without giving Phase 4 any inputs to work from — that is not credible.

When satisfied: *"Canvas closed. Moving to Phase 4 — Economic Viability, where we stress-test the revenue model and TVL scenarios."*
