# Protocol Init
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

Create the `.specs/` directory tree and populate `PROTOCOL.md` and `STATE.md`.

## Scope of this phase

Protocol init covers: name, category, network, greenfield/fork, upgrade strategy. Nothing else.

If the developer raises questions about economics (incentive mechanisms, parameter values, math models), architecture (contract structure, access control), or security (attack vectors, threat scenarios) during init — register as `QUEUED [Phase N]: ...` in STATE.md and redirect back. Do not discuss in depth. These are Phase 2–4 topics and will be addressed with full context when those phases begin.

---

## Steps

### 1. Identify the protocol — three rounds

Do not ask all questions at once. Work through three rounds, waiting for answers before advancing.

---

**Round 1 — Identity** (ask only these two):

- What is the protocol name?
- What is the category? — *The category determines which canonical implementations to study in Phase 1 and what invariant patterns are typical.* (AMM, lending, prediction market, yield optimizer, perpetuals, bonding curve, synthetic assets, Uniswap v4 hook, other)

After getting answers: acknowledge what you understood and say explicitly what the category tells you — which canonical implementations will be relevant, what invariant patterns are typical for that category. This is not filler — it gives the developer a chance to correct a wrong category read before proceeding.

Example: *"Understood. FloorHook falls under AMM + Protocol-Owned Liquidity. In Phase 1 we will primarily study the Uniswap v4 hook architecture and existing floor protocols. Typical invariants for this category revolve around floor range integrity and TWAP manipulation resistance. Does that sound right?"*

If the protocol spans multiple categories, list all of them and confirm.

---

**Round 2 — Context** (ask only these two):

- What is the network? — *The network affects block time (which sizes the TWAP window and N-block triggers), the sequencer trust model, and gas costs in hot paths like swaps and liquidations.* (Ethereum mainnet, Base, Arbitrum, Optimism, multi-chain)
- Greenfield or modification of something existing? — *This skill currently supports greenfield projects only. If the answer is modification, stop here.*

**If the answer is modification**: stop and say exactly this:

> "This skill currently supports greenfield projects only. Modifications to existing codebases require mapping the existing contract state, storage layout, and deployed invariants before specifying — that path is not yet covered. When modification support is added, it will be a separate flow in a future version."

Do not proceed to Round 3. Do not write any files.

After getting answers (greenfield confirmed): if it's a known L2, note any implications for the spec (e.g. block time affects TWAP window sizing; L2 sequencer trust model affects certain threat vectors). Keep this brief — 1-2 sentences, not a lecture.

---

**Round 3 — Upgrade strategy** (one decision, context-first):

Before asking, explain why this matters — one sentence:

*"The upgrade strategy determines the audit scope, the governance model, and how users trust the protocol — worth deciding now rather than assuming the boilerplate default."*

Then **recommend based on what you already know** from Rounds 1 and 2. Don't present a neutral menu — use the context:

- If the protocol has a timelock, floor lock, or any mechanism that promises users the code won't change: *"Given the [floor lock / timelock / mechanism X], immutable is the coherent choice — an upgradeable contract would contradict that promise to users. Confirm, or do you want to explore proxy options?"*
- If the protocol is complex, multi-contract, or mentioned governance: *"For a system with [N contracts / on-chain governance], UUPS proxy is usually the right balance — upgrade logic in the implementation, no storage collision risk from transparent. Confirm, or do you prefer immutable?"*
- If no strong signal either way: *"For [category], the most common is [immutable / UUPS]. What is the strategy, and who controls upgrades — EOA, multisig, DAO?"*

Only expand to the full options list if the developer is undecided or asks to see alternatives:
- **Immutable** — smallest attack surface; bugs require migration
- **UUPS proxy** — upgrade logic in the implementation; cleaner than transparent, requires care in `_authorizeUpgrade`
- **Transparent proxy** — clear proxy/implementation separation; storage collision risk in complex inheritance chains
- **Beacon proxy** — for multiple instances of the same contract
- **Diamond (EIP-2535)** — for very large modular contracts

---

The protocol category is load-bearing — it determines which canonical implementations to study in Phase 1, what invariant patterns are typical, and what threat model frameworks apply. If the protocol spans multiple categories (e.g. a lending protocol with an integrated AMM), list all categories.

### 2. Create the directory tree

```
.specs/
├── project/
├── research/
├── economics/
├── architecture/
├── threat-model/
├── interface/
└── tests/
```

### 3. Preview PROTOCOL.md before writing

Before writing, give a one-sentence preview of what the file will capture — the category, the key design goals, the upgrade strategy, and any inferences you made from the developer's description that weren't explicitly stated.

Example: *"Writing PROTOCOL.md with FloorHook as Uniswap v4 hook + POL, immutable given the 2-year floor lock, Base as network, and I will open two questions in STATE.md about the TWAP window and the permissionless ratchet. Ok?"*

If the preview contains inferences the developer hasn't explicitly confirmed, end with a concrete question about the most important one — not a generic "Ok?". Only stop for inferences that would meaningfully change the spec if they're wrong. Obvious category defaults (e.g. ERC-4626 for a yield optimizer, concentrated liquidity for a Uni v4 hook) don't qualify — mention them in the preview but don't require confirmation.

If everything was explicitly decided in the rounds, skip the confirmation and write directly.

Example of a meaningful checkpoint — inferences that change the spec if wrong: *"Writing PROTOCOL.md with FloorHook as AMM + POL, immutable, Base. Two inferences I made that you did not confirm: I classified the ratchet as permissionless and listed 'no governance' in non-goals — correct?"*

Example of a non-meaningful checkpoint (skip, just mention): *"Writing PROTOCOL.md with the data from the three rounds. Included ERC-4626 as the vault standard — standard for this category."* → write directly.

### 4. Create PROTOCOL.md

```markdown
# [Protocol Name]

## Category
[AMM / lending / prediction market / yield optimizer / perpetuals / other]

## Network
[Ethereum mainnet / Arbitrum / Base / multi-chain / etc.]

## Overview
[One paragraph: what the protocol does and who it's for]

## Design Goals
- [Goal 1]
- [Goal 2]
- [Goal 3]

## Non-Goals
- [What this protocol explicitly does not do]

## Key External Dependencies
- [Oracle: Chainlink / Uniswap TWAP / other]
- [Token standards: ERC-20 / ERC-4626 / ERC-721 / etc.]
- [External protocols integrated: Uniswap v3 / Aave / Compound / etc.]

## Upgrade Strategy
[ ] Immutable deployment
[ ] Transparent proxy (OpenZeppelin)
[ ] UUPS proxy
[ ] Beacon proxy
[ ] Diamond (EIP-2535)
[ ] Other: ___

Decision rationale: [why this upgrade strategy]

## Status
Phase: [1 - Protocol Research]
Started: [date]
```

### 5. Create STATE.md

```markdown
# Protocol State

## Open Questions
<!-- Use structured options format when the question has clear choices — see state-management.md    -->
<!-- Every OQ must have a Blocking phase. If no specific phase is identified, use "Phase 6 at latest" -->
<!--                                                                                                 -->
<!-- Structured example (use when choices are clear):                                               -->
<!-- OQ-1 [2026-01-01]: Should oracle be Chainlink or TWAP?                                        -->
<!-- Option A: Chainlink — real-time, trusted, external dependency risk                            -->
<!-- Option B: Uniswap TWAP — on-chain, manipulation resistant, 30min latency                     -->
<!-- Recommendation: A — lower latency more important for liquidations than manipulation resistance -->
<!-- Risk if wrong: External feed failure = liquidations blocked                                   -->
<!-- Blocking phase: Phase 2 — cannot close until resolved.                                        -->
<!--                                                                                                 -->
<!-- Simple example (use when options not yet clear):                                               -->
<!-- OQ-2 [2026-01-01]: Yield source accrual timing — continuous or discrete?                      -->
<!-- Context: Affects MEV analysis for harvest timing.                                              -->
<!-- Blocking phase: Phase 6 at latest — must be CLOSED or ACCEPTED-AMBIGUITY before spec closes. -->

## Queued Discussions
<!-- Topics raised during init that belong to a future phase -->
<!-- Format: QUEUED [Phase N] [date]: description -->
<!-- Example: QUEUED [Phase 2] [2026-01-01]: Ratchet bounty mechanism — dev asked about gas viability of keeperReserve approach -->

## Current Discussion
<!-- What is actively being worked on right now. Updated in real-time. Cleared when the item closes. -->
<!-- Format: [item being closed] — blocking question: [what decision is needed] -->

## Expansion Queue
<!-- Threads raised during the current discussion that were parked to keep focus. -->
<!-- When Current Discussion closes, surface these and ask which to address next. -->
<!-- Format: [date]: [topic] — raised during [item] -->

## Decisions
<!-- Record of design decisions with rationale -->
<!-- Format: [date] DECISION: [what was decided] — Rationale: [why] -->

## Blockers
<!-- Things blocking progress that require external input or research -->

## Deferred Ideas
<!-- Good ideas explicitly deferred to v2 or later -->

## Phase History
- [ ] Phase 1 — Protocol Research
- [ ] Phase 2 — Economic Design
- [ ] Phase 3 — Architecture & Contracts
- [ ] Phase 4 — Threat Modeling
- [ ] Phase 5 — Interface, Storage & Events
- [ ] Phase 6 — Test Specification
- [ ] Implementation
```

### 6. Highlights, then transition

After producing PROTOCOL.md and STATE.md, don't ask "any adjustments?". Instead, name 2–3 specific things the developer should look at — decisions that were inferred from context, values that will need confirmation in a later phase, or structural choices with downstream consequences.

Example highlights:
- *"I classified the protocol as [X] — if that is wrong, Phase 1 will study the wrong canonical implementations."*
- *"Non-goals lists [Y] — confirm this is explicitly out of scope for v1, not just deprioritized."*
- *"There are [N] open questions in STATE.md that need to be closed before Phase 2."*

Then transition: "When ready, say **protocol research** to begin Phase 1."

## Done when — gate

Before advancing, name the path of each file created during init. If you cannot name it, it does not exist — create it now.

- `.specs/project/PROTOCOL.md` exists and is entirely in English
- `.specs/project/STATE.md` exists with Phase History, Open Questions, and Queued Discussions sections
- All QUEUED items mentioned verbally are written to STATE.md
- Repo name derived (kebab-case) and confirmed
