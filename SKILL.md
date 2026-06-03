---
name: defi-spec-driven
description: >
  DeFi protocol specification using a 6-phase spec-driven methodology before any Solidity is written.
  Use when: starting a DeFi protocol, designing smart contracts, building on EVM, planning economic
  invariants, threat modeling a contract system, specifying Solidity interfaces, or planning fuzz tests.
  Continues through implementation with per-function tracking against the spec.
  Triggers on: "initialize protocol", "defi spec", "solidity spec", "protocol research",
  "economic spec", "define invariants", "architecture spec", "design contracts", "threat model",
  "security spec", "interface spec", "function spec", "test spec", "implement protocol",
  "implement contract", "implement function", "pause work", "resume work", "quick fix".
license: CC-BY-4.0
metadata:
  author: Gil Lopes Bueno
  version: 1.0.0
---

# DeFi Spec-Driven Development

Specify completely. Implement deliberately. Ship knowing what you built.

```
┌─────────────┐   ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│  PHASE 1    │ → │  PHASE 2    │ → │  PHASE 3    │ → │  PHASE 4    │
│  Protocol   │   │  Economic   │   │ Architecture│   │   Threat    │
│  Research   │   │  Design     │   │ & Contracts │   │  Modeling   │
└─────────────┘   └─────────────┘   └─────────────┘   └─────────────┘
                                                               ↓
┌─────────────┐   ┌─────────────┐   ┌─────────────────────────────────┐
│  PHASE 6    │ ← │  PHASE 5    │ ← │  always required — never skip   │
│  Test Spec  │   │  Interface  │                                      │
│             │   │  & Storage  │                                      │
└─────────────┘   └─────────────┘                                      │
       ↓                                                               │
┌─────────────────────────────────────────────────────────────────────┘
│  IMPLEMENT — per-function loop against the spec
└─────────────────────────────────────────────────────────────────────
```

## Core Philosophy

Five things that make DeFi different from every other software domain:

1. **Immutability** — Deployed code is the contract. Hotfixes require governance, timelocks, and re-audits. Every design decision is load-bearing.
2. **Economic stakes** — Every state-changing function is a potential attack surface. Logic errors and economic exploits are the same category of problem.
3. **Composability** — Other protocols integrate yours in ways you didn't design for. Unintended behaviors become attack vectors when composed with others at scale.
4. **Adversarial environment** — Sophisticated actors will analyze your bytecode for hours before you announce launch. The spec is the adversary's first defeat.
5. **AI as amplifier, not substitute** — AI working from a precise spec produces code you can review with confidence. AI working from a vague prompt produces plausible-looking code with hidden assumptions you can't audit.

## Phase Overview

| Phase | Name | Depth | Output files |
|---|---|---|---|
| 1 | Protocol Research | Always full — no safe abbreviation | `RESEARCH.md` |
| 2 | Economic Design | Always full | `INVARIANTS.md`, `ECONOMICS.md` |
| 3 | Architecture & Contracts | Always full | `CONTRACTS.md`, `ACCESS-CONTROL.md`, `ONCHAIN-OFFCHAIN.md` |
| 4 | Threat Modeling | Always full — function by function | `THREAT-MODEL.md` |
| 5 | Interface, Storage & Events | Can streamline for simple/internal protocols | `FUNCTIONS.md`, `STORAGE.md`, `EVENTS.md` |
| 6 | Test Specification | Can streamline for prototypes | `FUZZ-TARGETS.md`, `SCENARIOS.md`, `UNIT-TESTS.md` |
| — | Implementation | Per-function execute loop | `IMPL-TASKS.md` per contract |

Phases 1–4 are the non-negotiable core. The economic model and threat model are where the money is — they have no streamlined version.

## Project File Structure

```
.specs/
├── project/
│   ├── PROTOCOL.md         # Protocol category, overview, design goals
│   └── STATE.md            # Decisions, open questions, blockers, deferred ideas
├── research/
│   └── RESEARCH.md         # Canonical implementations studied, post-mortems reviewed
├── economics/
│   ├── INVARIANTS.md       # Formal invariants with INV-* slugs
│   └── ECONOMICS.md        # Incentive model, stress tests, oracle dependencies
├── architecture/
│   ├── CONTRACTS.md        # Contract system, responsibilities, interactions
│   ├── ACCESS-CONTROL.md   # Role model, key holders, timelocks, emergency functions
│   └── ONCHAIN-OFFCHAIN.md # On-chain/off-chain data boundaries
├── threat-model/
│   └── THREAT-MODEL.md     # Per-function: value at risk, attack vectors, mitigations
├── interface/
│   ├── FUNCTIONS.md        # REQ-* slugs, signatures, pre/post-conditions, reverts
│   ├── STORAGE.md          # Slot-by-slot layout, gap strategy
│   └── EVENTS.md           # Events and custom errors with triggering conditions
└── tests/
    ├── FUZZ-TARGETS.md     # INV-* mapped to fuzz test targets
    ├── SCENARIOS.md        # Gherkin attack scenarios and multi-step flows
    └── UNIT-TESTS.md       # Gherkin happy paths and critical reverts
```

During implementation, each contract also gets an `IMPL-TASKS.md` tracking per-function status.

## Context Loading Strategy

**Always loaded** (base context):
- `PROTOCOL.md` — protocol identity and category
- `STATE.md` — current decisions, open questions, session continuity

**Loaded on-demand** (load only when working on that phase):
- The reference file for the active phase
- The output file(s) for the active phase
- `IMPL-TASKS.md` for the contract currently being implemented

**Never load simultaneously**: multiple phase output files, multiple contract specs. Keep total context under 40k tokens — the remaining space is for reasoning and output.

## Commands

| Trigger | Phase | Reference |
|---|---|---|
| initialize protocol, new defi project | Init | [protocol-init.md](references/protocol-init.md) |
| protocol research, research phase | Phase 1 | [research.md](references/research.md) |
| economic spec, define invariants, economic design | Phase 2 | [economics.md](references/economics.md) |
| architecture spec, design contracts, contract design | Phase 3 | [architecture.md](references/architecture.md) |
| threat model, security spec, threat modeling | Phase 4 | [threat-model.md](references/threat-model.md) |
| interface spec, function spec, specify interface | Phase 5 | [interface-spec.md](references/interface-spec.md) |
| test spec, test specification | Phase 6 | [test-spec.md](references/test-spec.md) |
| setup project, initialize foundry, create project | Setup | [project-setup.md](references/project-setup.md) |
| implement protocol, implement contract, implement function | Impl | [project-setup.md](references/project-setup.md) (if no project yet) + [implement.md](references/implement.md) + [impl-tasks.md](references/impl-tasks.md) |
| record decision, log blocker, open question | Any | [state-management.md](references/state-management.md) |
| pause work, resume work, continue | Any | [session-handoff.md](references/session-handoff.md) |
| quick fix, quick task, small change | Any | [quick-mode.md](references/quick-mode.md) |

## Interaction Principles

These apply every turn, regardless of phase.

**Language rule**: all spec documents (any `.md` file written to `.specs/`) are always in English. Conversation with the developer follows the developer's language. These never mix.

### 1. Defer out-of-phase questions

When a developer raises a question belonging to a future phase: (1) acknowledge briefly and name which phase addresses it, (2) register in STATE.md as `QUEUED [Phase N]: [description]`, (3) redirect back immediately. The full answer comes in the right phase, with the full context that phase provides.

Phase reference — what belongs where:
- **Init**: name, category, network, greenfield/fork, upgrade strategy
- **Phase 1**: canonical implementations, post-mortems, failure modes
- **Phase 2**: invariants, economic parameters, incentive mechanisms
- **Phase 3**: contract structure, access control, on/off-chain boundaries
- **Phase 4**: per-function attack vectors, mitigations
- **Phase 5**: function signatures, storage layout, events, errors
- **Phase 6**: fuzz targets, attack scenarios, unit tests

### 2. Gate before advancing

"Próximo invariante" / "próxima fase" triggers two checks:

**Check A — Current Discussion**: for each open sub-thread, require an explicit choice:
- **Blocks current item** → *"'X' bloqueia fechar [item]. Resolve agora ou vira OQ-N?"*
- **Tangent** → *"'X' está aberto. Resolve agora, vira OQ-N, ou Expansion Queue?"*

**Check B — Open Questions at phase boundary**: before closing any phase, check STATE.md for OQs whose `Blocking phase` matches the current phase. Each must be resolved before advancing. Additionally, before Phase 6 closes, any OQ still open — regardless of blocking phase — must be either CLOSED or explicitly converted to ACCEPTED-AMBIGUITY with documented assumption and risk. No OQ survives Phase 6 as silently unresolved.

Advance cleanly — after Current Discussion is empty and blocking OQs are resolved.

### 3. Closing format — embedded queue check

Every closing statement uses this structure — the Expansion Queue check is part of the sentence, not a separate step:

> *"[Item] fechado. [→ if Expansion Queue non-empty: 'Na fila: [items] — seguimos ou trabalhamos primeiro?']"*

### 4. Questions carry their "why"

Every question includes one clause explaining what the answer determines — inline, not after.

*"Greenfield ou modificação? — Se modificação, preciso mapear o código existente antes."*

### 5. Recommendations over menus

When context points to an answer, recommend and let the developer confirm. Only present options when the decision is genuinely open.

### 6. Preview before large outputs

Before producing a full file: one sentence stating what it will capture and any unconfirmed inferences. Only ask for confirmation if there are meaningful unconfirmed inferences — not for obvious category defaults. After producing: name 2–3 specific things needing the developer's eyes — decisions that were inferred, values that need confirmation, or choices with downstream consequences.

### 7. Expansion queue — park tangents

When a sub-question isn't a prerequisite for the current item: register in `Expansion Queue` in STATE.md and redirect. Exception: if the sub-question is an objection that could invalidate the current decision, address immediately — it may reopen the decision. Criterion: *"can the answer change what we're about to close?"* If yes, follow. If no, queue.

### 8. Autonomous checkpoint (no live developer turn between phases)

When advancing between phases without a developer message in between — running multiple phases in a single session — perform an explicit checkpoint before loading the next phase reference. Write this block literally before continuing:

```
CHECKPOINT — Phase N complete
Files created this phase:
- /path/to/file1.md
- /path/to/file2.md
Done when verified:
- [x] item 1 — confirmed: /path/to/file1.md exists, first heading: "..."
- [x] item 2 — confirmed: STATE.md Phase History shows [x] Phase N
- [ ] item 3 — NOT done → completing now before advancing
```

Do not load the next phase reference until every item in the checkpoint is `[x]`. The checkpoint is not a summary — it is a gate. If you cannot write the path of a required file, the file does not exist.

---

## Key Conventions

### Slug-based requirement IDs

Every requirement and invariant gets a stable slug identifier — never sequential numbers (they break when requirements are added or removed).

- Requirements: `REQ-{verb}-{noun}` — e.g. `REQ-withdraw-solvency`, `REQ-deposit-min-shares`
- Invariants: `INV-{noun}-{property}` — e.g. `INV-vault-solvency`, `INV-share-monotonic`

Slugs must appear in three places: the spec file, the implementation comment, and the test file. This traceability chain is what makes the spec auditable.

When naming is slow, ask the LLM to generate a slug from the requirement description and review it. Consistent naming takes seconds to review and survives months of maintenance.

### SPEC_DEVIATION marker

When implementation diverges from spec — because something discovered during coding that the spec didn't anticipate — mark it explicitly:

```solidity
// SPEC_DEVIATION: REQ-withdraw-solvency
// Spec assumed CEI pattern sufficient; added nonReentrant due to ERC-777 token risk.
// Spec updated: 2025-03-10
```

Never silently update spec or code without marking the gap. Auditors treat these as high-priority review points. The spec gets updated after the fact with the reasoning intact.

### UNDOCUMENTED CODE PATH marker

For logic that the spec doesn't mention at all — not a deviation from stated behavior, but behavior that was never stated:

```solidity
// UNDOCUMENTED CODE PATH: zero-share edge case on first deposit
// Not in spec. Discovered during implementation. Adds 1000 dead shares to prevent
// inflation attacks. Spec to be updated before audit.
```

### Open questions discipline

When a question cannot be answered with confidence during any phase, it goes into `STATE.md` as an explicit open question — never silently resolved with an assumption. Every open question must be explicitly closed (with a decision and rationale recorded) before the next phase begins.

An unresolved ambiguity in the economic model has a way of becoming an implicit assumption in the code. Implicit assumptions are where edge cases hide.
