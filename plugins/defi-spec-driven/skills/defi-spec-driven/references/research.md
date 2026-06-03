# Phase 1 — Protocol Research
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Deeply understand what you're building and what has been built before — in the same category — before writing a single spec line.

Load: `PROTOCOL.md` (for protocol category), `STATE.md` (for open questions)
Output: `research/RESEARCH.md`

---

## What this phase produces

A documented understanding of:
- The canonical implementations in your protocol category and why their key design decisions were made
- The most relevant exploit post-mortems and what patterns they reveal
- Design constraints and known failure modes specific to your category
- Any regulatory or legal considerations surfaced early

This is not research for its own sake. Every item recorded here informs a decision in Phase 2 or 3 or a threat vector in Phase 4.

---

## Workflow acceleration (optional)

Phase 1 involves studying multiple independent sources simultaneously — canonical implementations and post-mortems are completely parallel work. If multi-agent workflows are available in your Claude Code environment, this phase can run significantly faster:

```
run a workflow to research [protocol category] canonical implementations and post-mortems,
producing a RESEARCH.md with findings from each source
```

The workflow spawns one agent per source, all running concurrently, and merges the results. For a protocol with 4 canonical implementations and 5 post-mortems, this compresses ~1 hour of sequential research into ~10 minutes.

If workflows aren't available, work through the steps below sequentially — the output is identical.

---

## Step 1: Identify canonical implementations

Based on the protocol category from `PROTOCOL.md`, identify the 2–4 most important prior implementations to study:

| Category | Canonical implementations to study |
|---|---|
| AMM | Uniswap v2 (constant product), Uniswap v3 (concentrated liquidity), Curve (stableswap) |
| Lending | Compound v2, Aave v2/v3 |
| Yield optimizer | Yearn v2/v3, Beefy |
| Prediction market | Polymarket, Augur v2 |
| Perpetuals / derivatives | GMX v1/v2, dYdX v3/v4 |
| Synthetic assets | Synthetix v2/v3 |
| Bonding curve / token launch | Bancor, Pump.fun architecture |
| Liquid staking | Lido, Rocket Pool |

For hybrid or novel protocols, identify the closest analogues across categories.

**How to study them**: Read the actual source code, not just the docs. For each canonical implementation, pick 1–3 specific design decisions that aren't obvious and understand *why* they were made.

**Use LLM actively**: Paste a contract section into Claude and ask it to walk through a specific design decision — why this function is structured this way, what attack the code is defending against, what invariant this block of logic is enforcing. This turns passive reading into active comprehension.

---

## Step 2: Read the relevant post-mortems

Post-mortems from DeFi exploits are some of the most valuable technical documents in the ecosystem. They tell you exactly what went wrong, under what conditions, and why the code didn't catch it.

**How to prioritize**: Describe your protocol to the LLM and ask it to list the most significant exploits in your category, the mechanism of each, and which patterns are most relevant to your design. You won't read every post-mortem — but you'll know which ones actually matter for what you're building.

**Primary sources**:
- Rekt.news (narrative + loss figures)
- BlockSec blog and audit publications
- Individual team post-mortems (usually on Mirror or official blogs)
- Immunefi disclosed reports

For each post-mortem studied: record the mechanism and whether your protocol design is susceptible to the same pattern.

---

## Step 3: Document in RESEARCH.md

```markdown
# Protocol Research

## Canonical Implementations Studied

### [Protocol Name] — [Category]
- **Why studied**: [Relevance to your design]
- **Key design decisions understood**:
  - [Decision 1]: [Why it was made, what problem it solves]
  - [Decision 2]: [Why it was made, what problem it solves]
- **Patterns adopted**: [What you're taking from this design]
- **Patterns avoided**: [What you're explicitly not doing and why]

## Post-Mortems Reviewed

### [Exploit Name] — [Date] — [$X lost]
- **Mechanism**: [How the exploit worked]
- **Root cause**: [What design assumption was wrong]
- **Relevance to our protocol**: [Susceptible / Not susceptible / Partially relevant]
- **Mitigation if relevant**: [What we need to address in Phase 3 or 4]

## Known Failure Modes for [Category]

[Summary of the most important failure patterns for this protocol category,
synthesized from the research above]

## Open Questions Surfaced

[Any questions that came up during research that need to be resolved in Phase 2 or 3]
→ Add these to STATE.md as well
```

---

## Step 4: Surface open questions to STATE.md

Any question that came up during research that you can't answer with confidence — about your own protocol's design choices, about economic assumptions, about integration behavior — goes into `STATE.md` as an explicit open question before Phase 2 begins.

Do not silently resolve research questions with assumptions. An unresolved question in Phase 1 becomes an unexamined assumption in the economic model.

---

## Regulatory note

For protocols involving token issuance, derivatives, or anything that could be classified as a financial instrument in major jurisdictions: flag the regulatory considerations early. Not to become a lawyer — but to avoid designing yourself into a corner that becomes a legal problem at launch.

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- `RESEARCH.md` is populated with at least the canonical implementations and post-mortems most relevant to your category
- All open questions surfaced during research are logged in `STATE.md`
- You can articulate in your own words why the canonical implementations made their key design decisions
- You know which post-mortem patterns are relevant to your protocol and what mitigations they imply
- `STATE.md` Phase History updated: `[x] Phase 1 — Protocol Research`

Transition: "Phase 1 complete. Say **economic spec** to begin Phase 2."
