# Phase 2 — Landscape & Analogues

## Goal

Map who has tried to solve this problem (or a closely related one) and what happened. Identify competitors, differentiation axes, and the early adopters most likely to move first.

A landscape that only lists competitors is incomplete. The most useful part is the analogue/antilog analysis — learning from protocols that succeeded or failed in adjacent space before writing a single line of spec.

---

## Step 1 — Direct competitors

List protocols that are solving the same problem for the same segment today. For each:

- Name, chain(s), TVL (current or peak)
- Core mechanism (one sentence)
- Fee structure and revenue
- Primary differentiation from each other

If there are no direct competitors: this is a signal worth examining. Ask: *"Is this space empty because the problem isn't real, because timing was wrong before, or because no one has tried yet? Each has a different implication."*

---

## Step 2 — Indirect competitors

Protocols that solve the same problem differently, or solve an adjacent problem that competes for the same user budget or mindshare.

Also include: manual solutions (doing it with scripts, keeper bots, or just accepting the loss), and CeFi alternatives (if a CeFi version exists and is good enough, it's competition).

---

> **Precondition check (if applicable)**: If the current concept's mechanism depends on an external ecosystem condition (a specific protocol's TVL, a wallet standard's adoption, a chain's validator set size), check now whether the competitive data just collected confirms that condition is currently met. If the data reveals the condition is NOT met and Phase 1 did not flag this: log it immediately as `OQ-N blocking Phase 3` and note in STATE.md: *"Phase 3 should apply the bifurcated v1/v2 pattern."* Do not wait for the gate.

---

## Step 3 — Analogue analysis

Analogues are protocols that solved a *different* problem using a *similar mechanism* — and succeeded. The value is in understanding what made the mechanism work, independently of the specific problem.

For each analogue:
- What mechanism did they use?
- What were the conditions that made it succeed? (timing, liquidity environment, team, first-mover)
- What did they get wrong initially that they had to fix?
- What's transferable to the current concept?

Look for 2–3 strong analogues. If none come to mind, ask: *"What's the closest thing to this mechanism that exists in DeFi or TradFi, even if the domain is different?"*

---

## Step 4 — Antilog analysis

Antilogs are protocols that tried something similar and failed — or succeeded but in a way you explicitly don't want to replicate (e.g., Olympus-style growth that was unsustainable).

For each antilog:
- What did they build?
- What specifically broke? (not "market conditions" — the specific mechanism failure)
- Was the failure inherent to the mechanism, or contingent on choices that can be made differently?
- What does this tell you about risks in the current concept?

DeFi has rich post-mortem material. The list below is illustrative — extend it with recent failures relevant to the specific domain.

Common useful antilogs by category:

**Stablecoins and lending:** Iron Finance (reflexive collateral), Anchor Protocol (unsustainable yield), Euler Finance (flash loan attack on donation mechanism), LUNA/UST (undercollateralized algorithmic stablecoin).

**LP vaults and AMMs:** Gamma Strategies Jan 2023 (spot-price share valuation exploited via flash loan — lesson: vault share math must use TWAP, not spot price), Visor Finance Dec 2021 (reentrancy + missing access control on vault withdraw — lesson: LP vault attack surface is elevated vs simpler vaults), Indexed Finance 2021 (AMM pool rebalance price manipulation — lesson: pool weight changes create manipulation windows).

**Bridges and cross-chain:** Nomad bridge (improper message validation), Ronin bridge (compromised validator key management), Wormhole (signature verification exploit).

Before completing this step, search "[protocol category] exploit" or "[protocol category] failure" for failures relevant to the specific domain — extend the list above with what you find. This search is always required; the list above is illustrative, not exhaustive. If the search returns no failures relevant to this specific domain: state that explicitly in the Antilog Analysis section — "Web search for [category] exploit / failure: no relevant recent failures found as of [date]." Do not omit the result even when null.

If the current concept shares any structural similarity with a known failure mode, surface it explicitly as an `ASM-*` entry in Phase 5. Don't wait.

---

## Step 5 — Differentiation map

Build a differentiation map: for each dimension that matters in this space, place the current concept and each competitor.

Common dimensions in DeFi:
- Decentralization level (permissionless vs curated vs centralized)
- Capital efficiency
- Risk profile for LPs/users
- Fee structure (flat / percentage / dynamic)
- Chain coverage (single-chain vs multichain vs chain-agnostic)
- Composability (pluggable vs standalone)
- Governance model

Pick the 3–4 dimensions most relevant to this segment. Place every actor on each. The current concept's position should be intentional — not "we're in the middle of everything."

**Required 2D positioning statement**: after filling the table, write one short paragraph naming the two most important dimensions, which quadrant is unoccupied, and why the current concept sits there. The table covers all dimensions; the 2D statement is what the Phase 3 gate check ("UVP distinct on at least two dimensions from the differentiation map") is actually verifying. A table without a 2D statement doesn't satisfy that gate.

---

## Step 6 — Early adopters

Early adopters are the first ~100 users or protocols that would move to this protocol before it has a proven track record. They need the protocol to exist more urgently than the average user.

Ask:
- *"Who is most underserved by the current alternatives — not just 'has the problem' but 'can't solve it well today'?"*
- *"Who would take on smart contract risk of a v1 protocol to get access to this?"*
- *"Are there protocols (DAOs, treasuries, yield strategies) that would integrate this from day one if it existed?"*

Early adopters are not the eventual TAM. They are the people who make bootstrapping possible.

---

## LANDSCAPE.md Template

```markdown
# Competitive Landscape

## Direct Competitors
| Protocol | Chain | TVL | Mechanism | Fee | Key strength |
|---|---|---|---|---|---|
| [name] | [chain] | [$] | [one sentence] | [structure] | [what they do best] |

## Indirect Competitors
[List with brief note on why each competes for the same user]

## Differentiation Map
Dimensions: [D1], [D2], [D3]

| Protocol | D1 | D2 | D3 |
|---|---|---|---|
| Current concept | [position] | [position] | [position] |
| [Competitor A] | ... | ... | ... |

## Analogue Analysis
### [Analogue 1 name]
Mechanism: [what they did]
Success conditions: [what made it work]
Transferable insight: [what applies here]

### [Analogue 2 name]
...

## Antilog Analysis
Web search result: [Web search for [category] exploit / failure: [findings summary or "no relevant recent failures found as of [date]"]]

### [Antilog 1 name]
What broke: [specific mechanism failure]
Inherent or contingent: [inherent to mechanism / contingent on choices]
Implication for current concept: [what to avoid or watch for]

### [Antilog 2 name]
...

**Structural similarity to known failure modes**: [None identified / See QUEUED entries: ASM-X, ASM-Y]

## Early Adopters
For each early adopter:
- **Name**: [specific protocol or named segment — not a category]
- **Current pain**: [what specifically are they experiencing today because this doesn't exist?]
- **Current workaround**: [what are they doing instead, and why is it insufficient?]
- **Adoption signal**: [specific public evidence that this entity currently has the problem and is looking for a solution — forum post, governance proposal, public statement, audit finding, or revealed operational behavior. "This type of entity generally needs X" is not an adoption signal — name a specific protocol or individual and cite specific evidence.]

## Key Findings
[2–3 sentences: what the landscape tells you about where to position and what to avoid]
```

---

## Advancing to Phase 3

Gate check before advancing:

1. Are direct and indirect competitors mapped? If not, stay.
2. Are at least 1 analogue and 1 antilog analyzed? If not, stay (if no analogues exist, document why and advance with that noted).
3. Is the early adopter segment identified specifically? If not, stay.
4. Did the antilog analysis surface any structural similarities to known failure modes? If yes, ensure they're queued for Phase 5 (`QUEUED [Phase 5]: ASM-* entry for [failure mode similarity]`). If no structural similarities were found: state that explicitly — do not silently pass this item.

5. If Phase 1 flagged a market precondition hypothesis (check STATE.md), or if the precondition check after Step 2 surfaced one: has the landscape analysis confirmed or refuted the precondition? To determine which branch applies — check current on-chain or ecosystem data (TVL in the relevant protocol, active adapters/hooks count, wallet standard penetration — whichever the mechanism depends on). If the current level is at least 20% of the threshold at which this mechanism becomes practically useful (use Steps 1–2 competitive data as calibration), treat as sufficient scale. If below that or if the threshold is unknown, treat as insufficient and log an OQ to establish the threshold.
   - Ecosystem at sufficient adoption scale: clear the OQ and remove the STATE.md v1/v2 note.
   - Insufficient adoption scale confirmed: verify the `OQ-N blocking Phase 3` and STATE.md v1/v2 note are still present.
   - Precondition concern first revealed by landscape analysis (not flagged in Phase 1 or the Step 2 check): log it now as `OQ-N blocking Phase 3` and add the note to STATE.md: *"Phase 3 should apply the bifurcated v1/v2 pattern."*

6. Is the differentiation map present in LANDSCAPE.md, with 3–4 dimensions filled for all competitors and a 2D positioning statement paragraph following the table? If not, complete Step 5 before advancing.

If the developer attempts to advance before all gate items are complete: complete the outstanding item before advancing. Do not ask permission to produce required outputs — phase gate items are not optional. If the developer explicitly pushes back, note in STATE.md that the item was completed over developer objection, then advance.

When satisfied: *"Landscape closed. Moving to Phase 3 — DeFi Lean Canvas, where we build the value proposition and unique mechanism."*
