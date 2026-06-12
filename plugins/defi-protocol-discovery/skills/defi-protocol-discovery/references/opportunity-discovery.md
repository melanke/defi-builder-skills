# Phase 0 — Opportunity Discovery

This phase is optional. Run it for Profile B (vague direction) and Profile C (open exploration). Skip it for Profile A (concrete idea).

## Goal

Produce a shortlist of 3–5 protocol concepts, each with a problem hypothesis. The developer selects one — that selected concept becomes the input to Phase 1.

A concept that survives Phase 0 has a named problem, a named segment with that problem, and a rough sense of why now. Nothing more is required at this stage.

## Focused Mode (Profile B)

The developer has named a space or segment (e.g., "something for LPs", "lending on a new chain", "yield for DAOs").

**Step 1 — Anchor**

Confirm the stated space precisely. Ask one clarifying question if the space is ambiguous:
*"When you say [X], do you mean [interpretation A] or [interpretation B]? — This determines which user problems and competitors we map."*

**Step 2 — Pain scan within the space**

Walk through the named space systematically using the User Segment → Pain Map technique:

For the target segment, identify their current friction points across three categories:
- **Execution friction**: things that are hard, slow, or expensive to do
- **Risk exposure**: risks they carry that they'd rather not
- **Opportunity cost**: value they're leaving on the table

Produce 5–8 pain points. For each, note: who has it, how acute it is (frequent vs rare, high-stakes vs low-stakes), and what current workarounds exist (if any).

**Step 3 — Filter to protocol-shaped problems**

Not every pain point maps to a DeFi protocol. Apply two filters:

1. *Is this a coordination problem that smart contracts can solve?* (If the solution requires trust, counterparty matching, or rule enforcement, it's a candidate.)
2. *Is there a sustainable economic model?* Two cases:
   - **Fatal flag (eliminate)**: no one would pay fees, OR the only revenue is token inflation with no stated path to real yield. Remove from the shortlist.
   - **Concern flag (keep, mark weak point)**: a fee model exists but has structural questions (thin margin, pricing difficulty, competitive compression, depends on a market condition not yet confirmed). Keep the candidate but write the concern into the Weak Point field — it becomes the first hypothesis Phase 1 stress-tests.

Keep 3–5 candidates after filtering. If more than 5 pass: rank remaining candidates by (1) problem specificity — the more concrete the customer and pain, the higher; (2) 'why now' strength — the more the present moment is distinctly favorable, the higher. Take the top 3–5. Document briefly why ranked-out candidates were dropped.

When writing each surviving candidate to OPPORTUNITIES.md, set the Filter status field: 'Passed — no economic concerns' if no flag was raised, or 'Concern flag: [brief note]' if a concern was raised during filtering. Use the same concern wording from the filtering step — do not write a new description.

**Step 4 — Shortlist with hypotheses**

For each surviving candidate, write a two-sentence hypothesis:
- *[Segment] has a problem with [pain]. A protocol that [mechanism] would solve this because [reason this is better than existing alternatives].*

Output `OPPORTUNITIES.md` with the shortlist.

---

## Open Mode (Profile C)

The developer has no stated direction. Run a systematic opportunity scan across DeFi.

**Step 1 — Establish starting point**

Ask one question to find a constraint to anchor on:
*"Is there a type of user you care most about building for, or a DeFi domain you find most interesting? — Even a loose preference lets us focus the scan. If truly open, say so and we'll cover the landscape."*

Use their answer to prioritize the scan — but cover all categories if they're genuinely open.

**Step 2 — Opportunity scan**

Work through the following lenses systematically. For each lens, generate 2–3 candidate opportunities before moving to the next. Present each lens's candidates before proceeding to the next — this allows the developer to react early and shape the remaining scan.

**Lens A — TradFi/CeFi gaps**
What financial instruments or mechanisms exist in traditional finance that have no good DeFi equivalent?
Examples: fixed-rate lending, credit scoring, RWA collateral, structured products, insurance.
For each gap: ask why it hasn't been built yet (technical barrier? regulatory? liquidity problem?) and whether those barriers are now lower.

**Lens B — Composability gaps**
What combinations of existing protocols create friction, require manual steps, or leave value on the table?
Look at: liquidity routing inefficiencies, yield aggregation that requires too much gas, cross-protocol liquidations, vault strategies that need rebalancing.

**Lens C — Post-mortem opportunities**
What protocols failed and left an unmet need? Failure doesn't invalidate a market — it may just mean the prior attempt had a specific flaw that's now avoidable.

Before generating candidates for this lens: search for recent DeFi protocol failures (query: "DeFi protocol exploit" or "DeFi protocol failure" filtered to the past 12–18 months, plus the specific domain being explored if one is in scope). The list of candidates comes from this search, not from a fixed reference — a static list goes stale and misses the failures most relevant to the current exploration.

Process at most 5–7 results. If more are returned: filter first to results whose failure domain overlaps the current scan direction (from Step 1); if still more than 7, rank by recency and take the most recent. If the scan is genuinely open with no direction, take the 5 most recent cross-domain failures. Document which results were examined and which were dropped.

For each retained failure, ask: (1) what specifically broke — mechanism, not "market conditions"; (2) is the unmet need still real today; (3) what has changed that would make a new attempt viable where the prior one failed.

**Lens D — Segment-specific gaps**
Pick 2–3 underserved segments and map their unmet needs:
- Protocol treasuries (idle USDC, yield without governance risk)
- LPs in concentrated liquidity (IL management, position rebalancing)
- Cross-chain users (bridging UX, fragmented liquidity)
- Institutional on-chain participants (compliance-aware DeFi, KYC pools)

**Lens E — Trend riding**
What infrastructure or user behavior is growing that creates new protocol opportunities?
Current relevant trends: EigenLayer restaking, account abstraction, intent-based architectures, RWA tokenization, onchain AI agents needing DeFi primitives.
For each: what protocol would be most useful in a world where this trend continues?

**Step 3 — Shortlist**

From all lenses combined, select 3–5 candidates applying the same two filters as Focused Mode (coordination problem shape + sustainable economics). For Lens E candidates, apply an additional disposition to filter 2: **Trend-contingent flag** (keep, mark weak point as trend-dependent) — the economic model is viable only if the named trend reaches sufficient scale before or at protocol launch. Write the Weak Point as: "Economic model depends on [trend name] reaching [specific threshold] — if adoption stalls, there is no fee-generating demand." This is distinct from a Concern flag; it is a structural dependency, not a structural question. A Lens E candidate with both a structural economic question and trend dependency receives both flags.

Before writing the Weak Point field for each surviving candidate, verify it meets the specificity standard: it must name (a) the entity whose behavior is assumed (e.g., "LPs", "protocol treasuries"), (b) the specific assumed behavior or condition (e.g., "will pay 5 bps to avoid IL"), and (c) why that assumption is not confirmed (e.g., "no protocol has demonstrated fee acceptance at this price point"). A weak point that names only a category of risk ("demand risk", "economic uncertainty") must be rewritten until it names a specific, testable claim.

Write two-sentence hypotheses for each. Output `OPPORTUNITIES.md`.

---

## OPPORTUNITIES.md Template

```markdown
# Protocol Opportunities

Discovery mode: [Focused / Open]
Starting point: [stated space or "open exploration"]

## Candidates

### OPP-1: [Working name]
**Problem**: [one sentence — who has what problem]
**Hypothesis**: [two sentences — mechanism + why better than existing]
**Why now**: [one sentence — what changed that makes this viable today]
**Weak point**: [one sentence — the most dangerous assumption in this hypothesis]
**Filter status**: [Passed — no economic concerns / Concern flag: [brief note on the structural question that remains]]

### OPP-2: [Working name]
...

### OPP-3: [Working name]
...

## Selected Concept
[OPP-N] — [Working name]
Reason for selection: [one sentence]
Primary weak point carried into Phase 1: [copy from OPP-N Weak Point field — Phase 1 will treat this as its first hypothesis to challenge]

## Eliminated Candidates
- [OPP-N]: [one-sentence reason for elimination]
```

---

## Presenting the Shortlist

Present the shortlist and give a recommendation — don't leave selection as an open menu:

*"My recommendation is [OPP-N]: the weak point ([weak point text]) is the most addressable of the batch, and the problem is the most specific. If [OPP-M] resonates more strongly with you, tell me why — that's useful signal about the direction you want to go."*

Give a recommendation even when the choice is close. The developer can override; what matters is that the reasoning is visible.

**If the developer asks for more detail on a candidate before selecting:**
Expand that candidate's problem, mechanism, and weak point in 2–3 focused paragraphs. Do not enter JTBD or 5 Whys — those are Phase 1. If they ask "but how would the mechanism work exactly?", redirect: *"The mechanism detail is Phase 1's job — we need to confirm the problem is worth solving before designing the solution. Right now the question is whether this problem is worth exploring."*

---

## Gate Before Advancing to Phase 1

Before loading the Phase 1 reference, confirm all four:

1. The selected concept has a named problem, a named segment, and at least one mechanism hypothesis. For Open Mode: the segment must be a defined population, not a segment class — "LPs providing liquidity in concentrated-liquidity AMMs on [chain/protocol]" is named; "LPs" or "LPs in concentrated liquidity" is a class. If the selected concept's segment is still a class, ask one clarifying question to sharpen it before clearing this gate item. If not resolvable: iterate within Phase 0.
2. The concept's primary weak point has been articulated and the developer has acknowledged it as a hypothesis to challenge in Phase 1. First verify the weak point meets the specificity standard from Open Mode Step 3 (named entity, named behavior, named reason it's unconfirmed). If it fails: surface the gap to the developer — *"Before testing your acknowledgment, the weak point itself needs sharpening — [current text] doesn't name a specific testable claim. What exactly has to be true about [entity] for this concept to work?"* Then apply the acknowledgment check on the revised weak point. Acknowledgment is sufficient if the developer either (a) offers a specific response to the weak point, or (b) explicitly accepts it as the primary hypothesis Phase 1 will challenge. A bare "yes", "ok", or "sounds good" is not sufficient — surface the weak point one more time and ask for engagement before clearing this gate. A specific response must contain at least one of: a concrete reason the weak point may not materialize, a named mitigation or design choice that addresses it, or an explicit restatement of the weak point as a named hypothesis (e.g., "Phase 1 will challenge whether [weak point text] is actually true"). The weak point substance must appear in the acknowledgment — naming the phase without naming the assumption is not sufficient.

Contrast: *"I think the demand is there"* clears the gate — it names the belief. *"Phase 1 will challenge whether LPs will actually pay 5 bps to avoid IL"* clears the gate — it restates the weak point as a testable hypothesis. *"Yes, that's a concern"* does not — it acknowledges concern without naming what will be challenged. *"Phase 1 will figure it out"* does not — it names the phase without naming the assumption.
3. All eliminated candidates are documented with a one-sentence reason in OPPORTUNITIES.md. If not: complete the file.
4. The developer understands Phase 1 will treat the selected hypothesis as a target to challenge, not a conclusion to document. State this explicitly before advancing: *"[OPP-N] selected. In Phase 1 we'll treat this as a hypothesis to challenge — the goal is to find the real problem beneath it, which may look different from what we've written here."*

When all four are satisfied, load the Phase 1 reference and begin.
