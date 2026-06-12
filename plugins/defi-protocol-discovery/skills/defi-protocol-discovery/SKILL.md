---
name: defi-protocol-discovery
description: >
  DeFi protocol opportunity discovery and viability assessment before committing to build.
  Use when: exploring what DeFi protocol to build, validating a protocol concept, assessing
  market fit and economic viability, mapping the competitive landscape, stress-testing an
  economic model, or making a go/no-go decision on a DeFi protocol.
  Triggers on: "discover protocol", "protocol discovery", "defi idea", "validate idea",
  "protocol viability", "defi canvas", "lean canvas", "should I build", "protocol opportunity",
  "find defi problems", "defi landscape", "protocol go/no-go", "discovery phase",
  "defi ideation", "protocol concept", "what should I build".
license: CC-BY-4.0
metadata:
  author: Gil Lopes Bueno
  version: 1.0.0
---

# DeFi Protocol Discovery

Discover the right problem. Validate the economics. Decide before you spec.

```
┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  PHASE 0    │ → │  PHASE 1    │ → │  PHASE 2    │ → │  PHASE 3    │
│  Opportunity│   │  Idea       │   │  Landscape  │   │  DeFi Lean  │
│  Discovery  │   │  Sharpening │   │  & Analogues│   │  Canvas     │
│  (optional) │   │             │   │             │   │             │
└─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
                                                               ↓
┌─────────────┐   ┌─────────────┐   ┌─────────────────────────────────┐
│  PHASE 6    │ ← │  PHASE 5    │ ← │  PHASE 4                        │
│  Go / No-Go │   │  Risk &     │   │  Economic Viability             │
│             │   │  Assumptions│   │                                 │
└─────────────┘   └─────────────┘   └─────────────────────────────────┘
       ↓
┌─────────────────────────────────────────────────────────────────────┐
│  → defi-spec-driven  (Protocol Brief becomes init input)            │
└─────────────────────────────────────────────────────────────────────┘
```

## Core Philosophy

Five things that make DeFi discovery different from generic startup validation:

1. **Economic exploits are product failures** — A weak economic model doesn't just disappoint users; it gets drained. Discovery must stress-test economics with an adversarial mindset, not just a market-fit lens.
2. **Liquidity is the product** — For most DeFi protocols, liquidity depth IS the value proposition. A viable concept must include a credible bootstrapping path, not just a revenue model.
3. **Composability is both moat and attack surface** — DeFi's interconnectedness creates channels, integrations, and reach — and cascade failures, oracle dependencies, and surface area. Both sides must be mapped.
4. **Timing is measurable** — Unlike most markets, DeFi has on-chain data. Competitive TVL, fee revenue, and volume are public. Assumptions can be grounded in real numbers, not just intuition.
5. **The cost of a bad idea discovered here is zero** — Bad code can be patched. Bad economic design gets exploited. This skill is the first gate. The spec is the second. Code is the third.

## Entry Routing

At initialization, detect the user's starting profile from their first message and route without asking which profile they are:

**Profile A — Concrete idea**: User describes a specific protocol concept.
→ Skip Phase 0. Load [idea-sharpening.md](references/idea-sharpening.md) and enter Phase 1.

**Profile B — Vague direction**: User knows a space or segment but has no specific idea yet.
→ Run Phase 0 in *focused mode* (anchored to stated space). Load [opportunity-discovery.md](references/opportunity-discovery.md).

**Profile C — Open exploration**: User wants to build in DeFi but has no direction.
→ Run Phase 0 in *open mode* (systematic opportunity scan). Load [opportunity-discovery.md](references/opportunity-discovery.md).

If the first message already clearly places the developer in Profile A, B, or C — route immediately without asking. If the first message doesn't contain enough signal to classify the profile, ask: *"Tell me about what you want to build — or about where you're thinking of building, if you don't have a specific idea yet."*

## Phase Overview

| Phase | Name | Mode | Reference | Output file |
|---|---|---|---|---|
| 0 | Opportunity Discovery | Optional (profiles B and C) | [opportunity-discovery.md](references/opportunity-discovery.md) | `OPPORTUNITIES.md` |
| 1 | Idea Sharpening | Always | [idea-sharpening.md](references/idea-sharpening.md) | `PROBLEM.md` |
| 2 | Landscape & Analogues | Always | [landscape.md](references/landscape.md) | `LANDSCAPE.md` |
| 3 | DeFi Lean Canvas | Always | [canvas.md](references/canvas.md) | `CANVAS.md` |
| 4 | Economic Viability | Always | [economics.md](references/economics.md) | `ECONOMICS.md` |
| 5 | Risk & Assumptions | Always | [risks.md](references/risks.md) | `RISKS.md` |
| 6 | Go / No-Go | Always | [decision.md](references/decision.md) | `DECISION.md` |

**Pivot discipline**: Phases 0 and 1 are pivot-friendly — looping back carries no cost and no logging requirement. From Phase 2 onward, pivoting requires an explicit decision recorded in STATE.md with the reason. Don't silently restart; document the change and continue forward.

Note: required phase outputs (files, gate items) are not pivots. If a developer tries to advance without completing a required output, complete the output before advancing — do not ask permission. Reserve pivot logging for concept direction changes, not for incomplete deliverables.

STATE.md is created at initialization using the template in [state-management.md](references/state-management.md). Load state-management.md at initialization to create STATE.md from the template, then keep it loaded for all STATE.md updates throughout the session.

## Project File Structure

```
.discovery/
├── project/
│   └── STATE.md               # Session continuity, decisions, open questions, expansion queue
├── opportunities/
│   └── OPPORTUNITIES.md       # Phase 0: ranked candidate shortlist (if run)
├── problem/
│   └── PROBLEM.md             # Phase 1: validated problem statement
├── landscape/
│   └── LANDSCAPE.md           # Phase 2: competitive map, analogues, antilogs, early adopters
├── canvas/
│   └── CANVAS.md              # Phase 3: DeFi-adapted lean canvas
├── economics/
│   └── ECONOMICS.md           # Phase 4: revenue model, TVL scenarios, unit economics
├── risks/
│   └── RISKS.md               # Phase 5: ranked assumptions, death spirals, validation plan
└── decision/
    └── DECISION.md            # Phase 6: verdict + Protocol Brief (if go)
```

STATE.md is created at initialization. All other files are created when their phase begins.

## Context Loading Strategy

**Always loaded** (base context):
- `STATE.md` — session continuity and decision log

**Loaded on-demand** (load only when working on that phase):
- The reference file for the active phase
- The output file currently being produced
- The previous phase's output (for continuity when starting a new phase)

**Never load simultaneously**: multiple phase reference files or multiple phase outputs. Keep total context lean — the remaining space is for the discovery conversation itself.

## Commands

| Trigger | Phase | Reference |
|---|---|---|
| discover protocol, what should I build, defi ideation | Init → Phase 0 | [opportunity-discovery.md](references/opportunity-discovery.md) |
| validate idea, sharpen idea, idea sharpening | Phase 1 | [idea-sharpening.md](references/idea-sharpening.md) |
| landscape, competitive map, analogues | Phase 2 | [landscape.md](references/landscape.md) |
| defi canvas, lean canvas, value proposition | Phase 3 | [canvas.md](references/canvas.md) |
| economic viability, revenue model, tvl scenarios | Phase 4 | [economics.md](references/economics.md) |
| risk assessment, assumptions, death spiral | Phase 5 | [risks.md](references/risks.md) |
| go/no-go, decision, protocol brief | Phase 6 | [decision.md](references/decision.md) |
| record decision, open question, log blocker | Any | [state-management.md](references/state-management.md) |
| pause, resume, continue | Any | [state-management.md](references/state-management.md) |

## Interaction Principles

These apply every turn, regardless of phase.

**Language rule**: all discovery documents (any `.md` file written to `.discovery/`) are always in English. Conversation follows the developer's language. These never mix.

### 1. Defer out-of-phase questions

When a developer raises a question belonging to a future phase: (1) acknowledge briefly and name which phase addresses it, (2) add to the Expansion Queue in STATE.md as `- [description] — queued from Phase [N]` (and dual-log as `OQ-N blocking Phase N` if it is also a blocking question), (3) redirect back immediately.

Phase reference — what belongs where:
- **Phase 0**: opportunity space, problem areas worth exploring, market gaps
- **Phase 1**: specific problem definition, target user, JTBD, problem depth
- **Phase 2**: competitors, analogues, antilogs, early adopters
- **Phase 3**: value proposition, unique mechanism, channels, revenue streams
- **Phase 4**: fee model, TVL projections, unit economics, bootstrapping cost
- **Phase 5**: ranked assumptions, death spirals, validation experiments
- **Phase 6**: go/no-go criteria, synthesis, Protocol Brief

### 2. Gate before advancing

"Next item" / "next phase" triggers two checks:

**Check A — Current Discussion**: for each open sub-thread, require an explicit choice:
- **Blocks current item** → *"'X' blocks closing [item]. Resolve now or log as OQ-N?"*
- **Tangent** → *"'X' is open. Resolve now, log as OQ-N, or Expansion Queue?"*

**Check B — Open Questions at phase boundary**: before closing any phase, check STATE.md for OQs whose `Blocking phase` matches the current phase. Each must be resolved before advancing. Before Phase 6 closes, every open OQ — regardless of blocking phase — must be either CLOSED or explicitly converted to ACCEPTED-AMBIGUITY with a documented assumption and its risk.

### 3. Closing format — embedded queue check

Every closing statement uses this structure:

> *"[Item] closed. [→ if Expansion Queue non-empty: 'Queued: [items] — continue or work through these first?']"*

### 4. Questions carry their "why"

Every question includes one clause explaining what the answer determines — inline.

*"Is the primary user a protocol or an end user? — This changes who 'channels' means in the canvas."*

### 5. Recommendations over menus

When context points to an answer, recommend and let the developer confirm. Only present options when the decision is genuinely open.

### 6. Preview before large outputs

Before producing a full file: one sentence stating what it will capture and any unconfirmed inferences. After producing: name 2–3 specific things needing the developer's eyes — inferred decisions, values needing confirmation, or choices with downstream consequences.

### 7. Expansion queue — park tangents

When a sub-question isn't a prerequisite for the current item, register in STATE.md `Expansion Queue` and redirect. Exception: if the sub-question is an objection that could invalidate the current decision, address it immediately. Criterion: *"can the answer change what we're about to close?"* If yes, follow. If no, queue.

For items that contain both a current-phase dimension (in scope for the active phase) and a future-phase dimension (out of scope): address the current-phase dimension immediately, then apply the dual-logging rule from state-management.md for the future-phase dimension — log as both an OQ with its blocking phase AND as an Expansion Queue entry. Don't defer both; don't address both immediately. Split them.

### 8. Challenge mode — don't just document

This skill's job is not to turn the developer's idea into a polished document. It is to find the weakest points in the idea before they become expensive. In every phase, identify the most dangerous assumption and surface it explicitly. A discovery process that never pushes back is not a discovery process — it's a writing exercise.

### 9. Kill criteria before synthesis

In Phase 6, establish kill criteria BEFORE synthesizing the go/no-go. Ask: *"What would have to be true for this to be a clear no?"* Confirm criteria first. Then synthesize against them. This prevents survivorship bias from creeping into the verdict.

### 10. Autonomous checkpoint

At every phase boundary — regardless of whether there was a developer message between phases — perform an explicit checkpoint before loading the next phase reference:

```
CHECKPOINT — Phase N complete
Files created this phase:
- /path/to/file.md
Done when verified:
- [x] item 1 — confirmed: /path/to/file.md exists, first heading: "..."
- [ ] item 2 — NOT done → completing now before advancing
- [ ] pivot check — if concept direction changed this phase: Pivot Log entry in STATE.md with all six fields? If no pivot occurred: N/A
```

Phase 2 checkpoint example:
```
CHECKPOINT — Phase 2 complete
Files created this phase:
- .discovery/landscape/LANDSCAPE.md
Done when verified:
- [x] file exists — first heading: "# Competitive Landscape"
- [x] analogue analysis — at least 1 entry with Mechanism + Transferable insight fields
- [x] antilog analysis — at least 1 entry with "What broke" field; web search result noted
- [x] early adopters — at least 1 named protocol with all four fields (Name, Current pain, Current workaround, Adoption signal)
- [x] differentiation map — table present AND 2D positioning statement paragraph follows
```

Do not load the next phase reference until every item is `[x]`.

---

## Key Conventions

### Assumption slugs

Every assumption gets a stable slug: `ASM-{domain}-{claim}`

Examples: `ASM-lp-demand`, `ASM-fee-sufficient`, `ASM-bootstrap-cost`, `ASM-oracle-reliable`

Slugs appear in `RISKS.md` (definition), the validation plan (test), and `DECISION.md` (status at go/no-go). This traceability makes the go/no-go auditable.

### Risk slugs

Every death spiral or systemic risk: `RISK-{trigger}-{consequence}`

Examples: `RISK-depeg-bankrun`, `RISK-oracle-manipulation-drain`, `RISK-incentive-collapse-liquidity`

### Open questions discipline

When a question cannot be answered confidently during a phase, log it in STATE.md — never silently resolve it with an assumption. Every OQ must be explicitly closed before Phase 6, or converted to ACCEPTED-AMBIGUITY with a documented assumption and risk level.

An unresolved ambiguity in the economic model becomes an implicit assumption in the spec. Implicit assumptions in the spec become attack vectors in the code.

### Protocol Brief format

The Protocol Brief in DECISION.md (produced on a GO or CONDITIONAL GO verdict) contains exactly:
1. Protocol name and category
2. Problem statement (from PROBLEM.md, one paragraph)
3. Target segment and early adopters
4. Regulatory status (CLEAR / REQUIRES CONSULTATION / TBD pending legal opinion on (specific question))
5. Core value proposition and unique mechanism
6. Economic model summary (revenue source, fee structure, TVL path)
7. Open assumptions still requiring validation post-launch
8. Handoff note: *"Start defi-spec-driven with: [one sentence framing for the init prompt]"*
