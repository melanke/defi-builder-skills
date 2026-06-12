# Phase 1 — Idea Sharpening

## Goal

Transform a raw idea or selected concept into a precise, defensible problem statement. By the end of this phase, the developer should be able to answer clearly: what specific problem, for whom, and why this matters enough to build a protocol.

`PROBLEM.md` produced here is the foundation that all subsequent phases build on. A vague problem statement produces a vague canvas, a vague economic model, and eventually a vague protocol. Push until the problem is crisp.

## Entry modes

**From Phase 0**: the concept hypothesis is already formed. Phase 1's job is to challenge it — find the assumption beneath the hypothesis that's most likely to be wrong, and test it through the techniques below. Begin by reading the Selected Concept section of OPPORTUNITIES.md and using the "Primary weak point carried into Phase 1" field as the first assumption to challenge — do not go back to the OPP-N candidate entry directly; the Selected Concept section is the authoritative handoff point. Frame JTBD Q1 (the trigger situation question) to probe that specific weak point — Q1 is the right level because it reveals whether the situation where the problem occurs is as described. Q2 and Q3 should follow from Q1's answer; do not pre-constrain them with the Phase 0 framing. If Q1's answer suggests the weak point was wrong, let Q2 and Q3 follow naturally from what Q1 revealed.

**Direct entry (Profile A)**: the developer has a raw idea. Phase 1 must first extract the problem (not the solution) before any sharpening can happen. Many developers arrive with a solution in mind and a problem they've only loosely defined. The first move is always: *separate problem from solution*.

---

## Step 1 — Separate problem from solution

If the developer describes a mechanism ("I want to build a vault that auto-rebalances positions"), extract the problem beneath it:

*"Before we go into the mechanism — what's the problem a user has today that this vault solves? What are they doing manually, or failing to do, that your protocol would handle for them?"*

If the developer's first message described both a mechanism and a clear problem statement, confirm the problem rather than re-asking: *"You described the problem as [X] — is that the core of it, or is there a more precise framing you'd use?"* Do not re-ask the extraction question if the problem is already stated.

This step is complete when the developer can state the problem independently of the solution. If they can't, stay here.

**Mechanism feasibility check**: once the problem is extracted, flag whether the mechanism described is confirmed (existing infrastructure, proven approach) or a hypothesis. Two distinct types require different logging:

- **Technical hypothesis**: depends on unproven primitives, novel combinations without clear precedent, or infrastructure that exists but hasn't been used this way before. Log as `OQ-N blocking Phase 3`.
- **Market precondition hypothesis**: the mechanism works technically but depends on an ecosystem condition that doesn't yet exist at sufficient adoption scale (e.g., V4 hooks require meaningful V4 TVL depth, intent-based architecture requires wallet penetration above a useful threshold). Log as `OQ-N blocking Phase 3` AND note in STATE.md: *"Phase 3 should apply the bifurcated v1/v2 pattern — mechanism depends on ecosystem precondition."* A market precondition is a viability-level concern, not just a technical flag — surface it with that weight.

In all cases, record the classification in STATE.md: `Mechanism type: [Technical hypothesis / Market precondition hypothesis / Both / Confirmed]`. This lets the Phase 2 gate verify the correct branch without relying on conversation recall. A mechanism can qualify as Both — note that explicitly. If the mechanism is confirmed (existing infrastructure, proven approach with clear precedent): record `Mechanism type: Confirmed` in STATE.md. No OQ required — advance without logging a blocker.

A mechanism that has never been examined for feasibility should not enter the canvas as if it were settled.

---

## Step 2 — Jobs-to-be-Done

Apply JTBD framing to sharpen the problem statement. A "job" is the progress a user is trying to make in a specific situation — not a feature they want.

Ask these three JTBD questions **one at a time** — wait for the developer's answer before moving to the next. The answer to Q1 shapes how Q2 is framed; the answer to Q2 shapes Q3. Batching all three in one message sacrifices the signal that makes JTBD useful.

1. *"When does someone decide they need to solve this problem? What triggers that moment?"*
   — This reveals the situation. A problem without a concrete trigger situation is often too abstract.

2. *"What does someone do today to make progress on this job — even if it's a bad solution?"*
   — The current workaround is always revealing. If there is no workaround, ask whether the problem is real. If the workaround is good enough, ask whether a protocol is actually needed.
   Note: if the developer's answer to Q1 already contained a clear answer to Q2 (they described both the trigger situation and the current workaround), confirm Q2's answer explicitly and move directly to Q3. Do not re-ask a question that was already answered — the sequential requirement prevents batching all questions upfront, not re-asking answered ones. If Q1's answer only partially addressed Q2 — it named a workaround but left its adequacy or prevalence unclear — ask one targeted follow-up before moving to Q3: *"You mentioned [workaround] — is that what most [segment] do today, or only some? This tells us whether the problem is acute enough to motivate switching."* Do not re-ask Q2 in full; address only the missing dimension.

3. *"What would make someone 'fire' their current solution and 'hire' yours?"*
   — This surfaces the switching cost and the specific improvement required. A protocol that's "a little better" rarely bootstraps liquidity.

---

## Step 3 — 5 Whys

Take the problem statement from Step 2 and apply 5 Whys to find the root cause. The goal is to verify that the stated problem is the real problem, not a symptom of something deeper.

Example:
- "LPs lose money to impermanent loss" → Why? → "Price divergence between assets"
- Why does price divergence matter? → "LP positions are static while prices move"
- Why are positions static? → "Rebalancing requires active monitoring and gas"
- Why is monitoring hard? → "No on-chain trigger mechanism exists for position adjustment"
- Root problem: absence of a reactive, gas-efficient position management primitive

At depth 3–5, the problem either becomes more specific (good) or reveals that the original framing was a symptom of something the developer isn't actually solving (important finding — log as OQ or trigger a pivot back).

---

## Step 4 — Segment sharpening

A problem that belongs to "everyone" belongs to no one. Identify the most specific segment that has this problem acutely.

Ask:
- *"Who has this problem most acutely — and would they adopt a protocol to solve it first?"*
- *"Is the primary user a human end-user, or a protocol/DAO (which integrates your protocol as infrastructure)?"*

The distinction between end-user and protocol-as-customer changes channels, pricing, bootstrapping strategy, and governance design. Make it explicit here.

Output: a named segment with a qualification (e.g., "LPs with concentrated positions on Uniswap V3 managing >$50k", "protocol treasuries holding idle USDC without governance approval requirements").

Do not close Step 4 until the segment includes at least one qualification dimension beyond the category name (size threshold, behavioral qualifier, governance constraint, or chain specificity). If the developer states only a category (e.g., "protocol treasuries", "retail LPs"), ask one narrowing question: *"What makes a [segment] in this category have this problem acutely — size, governance structure, risk tolerance, something else?"*

---

## Step 5 — Problem statement synthesis

Combine Steps 1–4 into a single structured problem statement:

```
[Segment] faces [problem] when [situation/trigger].
Today they [current workaround], which fails because [specific failure mode].
A protocol could solve this by [mechanism sketch — one sentence only].
The job-to-be-done: [active verb phrase — what progress the user is making].
```

The mechanism sketch is allowed here (one sentence) because it anchors the problem to a tractable solution space. It is not a commitment — it will be challenged in Phase 3.

---

## PROBLEM.md Template

```markdown
# Problem Statement

## Segment
[Specific segment with qualification]

Primary user type: [End-user / Protocol-as-customer / Both]

## Problem
[One paragraph: problem, situation, why it matters]

## Current Workarounds
[What people do today, and why it's insufficient]

## Job-to-be-Done
[Active verb phrase: the progress the user is trying to make]
Example: "Protect LP yield from directional price moves without exiting the position"

## Root Cause (5 Whys)
[The structural reason this problem exists — one sentence]

## Mechanism Sketch
[One sentence: how a protocol could address the root cause]
Note: this is a hypothesis, not a commitment. It will be tested in Phase 3.

## Open Questions
[Any unresolved questions about the problem definition, logged from this phase]
```

---

## Advancing to Phase 2

Gate check before advancing:

1. Can the developer state the problem independently of the solution? If not, stay.
2. Is the segment specific enough to identify early adopters? If not, stay.
3. Is the root cause identified (via 5 Whys)? If not, stay.
4. Are all blocking OQs from this phase resolved? If not, resolve or convert to ACCEPTED-AMBIGUITY.

5a. If a mechanism was described and flagged as a **technical hypothesis**: is there an `OQ-N blocking Phase 3` logged? If not, log it before advancing.

5b. If a mechanism was described and flagged as a **market precondition hypothesis**: verify both independently — (a) is there an `OQ-N blocking Phase 3` logged? AND (b) does STATE.md explicitly contain the note *"Phase 3 should apply the bifurcated v1/v2 pattern"*? A missing OQ or a missing STATE.md note each independently block advancement.

5c. If the mechanism was classified as **Both**: apply both 5a and 5b checks independently — all three conditions must be satisfied (OQ for technical, OQ for market precondition, STATE.md bifurcated-pattern note) before advancing.

When all applicable items are satisfied — items 1–4 always apply; items 5a/5b/5c apply only if a mechanism was described: *"Problem statement closed. Moving to Phase 2 — Landscape & Analogues, where we map who's tried this before and what we can learn from them."*
