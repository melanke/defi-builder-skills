# State Management

Persistent memory across sessions. STATE.md is the single source of truth for decisions, open questions, blockers, and deferred ideas.

---

## Recording a decision

When a significant design decision is made — one with non-obvious rationale or future consequences — record it immediately:

```markdown
## Decisions

[2025-03-10] DECISION: Use UUPS proxy pattern for VaultCore
Rationale: Transparent proxy would expose the implementation slot to storage collision risk
given the complex inheritance chain. UUPS moves upgrade logic to the implementation, reducing
surface area. Trade-off: upgrade function must be included in all future implementations.
Related: CONTRACTS.md — VaultCore upgrade strategy
```

Decisions worth recording: upgrade strategy choices, economic parameter selections, MEV mitigation approaches, access control trade-offs, oracle selections, anything that future-you or an auditor would wonder "why did they do it this way?"

---

## Logging an open question

When a question cannot be answered with confidence, it becomes an open question — not a silent assumption.

Open questions use sequential numeric IDs (`OQ-1`, `OQ-2`, ...) so the developer can reference them easily in answers ("respondendo OQ-2: ..."). The ID is only a conversation handle — it disappears when the OQ is closed.

Every OQ must have a `Blocking phase` — the earliest phase that cannot close without this question being resolved. There is no such thing as a permanently non-blocking OQ. If a question genuinely doesn't block any specific phase, it must be explicitly accepted as ambiguity before Phase 6 closes (see ACCEPTED-AMBIGUITY below).

**OQ format — use structured options whenever the question has clear choices:**

```markdown
## Open Questions

OQ-1 [2025-03-08]: Should liquidations be blocked when the oracle price is stale?
Option A: Block all liquidations on stale price
  — safe; avoids liquidating at wrong price; risk: positions cannot be liquidated during oracle downtime
Option B: Allow liquidations using last known price with a discount
  — keeps protocol solvent during oracle gaps; risk: liquidations may occur at unfavorable prices
Recommendation: A — liquidating at a wrong price is worse than a temporary gap in liquidations
Risk if wrong: Protocol may accumulate bad debt during extended oracle downtime
Blocking phase: Phase 3 (Architecture) — cannot close until this is resolved.
Prerequisite for: (add when a specific item is found to depend on this OQ — e.g. "INV-collateral-solvency cannot be closed until OQ-1 is resolved")
```

The `Prerequisite for:` field is optional at creation but should be added as soon as a specific invariant, decision, or spec entry is found to depend on the OQ. This makes the dependency explicit and visible rather than discovered mid-discussion.

If the question has no clear options yet (genuinely unknown), use the simpler form:

```markdown
OQ-2 [2025-03-08]: Does the yield source accrue continuously or in discrete steps?
Context: Affects MEV analysis for deposit/withdraw timing and harvest front-running risk.
Blocking phase: Phase 6 at latest — must be CLOSED or ACCEPTED-AMBIGUITY before spec is complete.
```

Use structured options whenever you can name at least two concrete choices — this makes the developer's decision a confirmation rather than an open-ended answer, which is faster and less error-prone.

Every OQ must have a `Blocking phase` at creation. If no specific phase is identified, default to `Phase 6 at latest` — meaning it must be resolved before the spec closes. This ensures no OQ is ever implicitly non-blocking.

Open questions must be closed (with a decision recorded) before the phase that depends on them can be completed.

---

## Accepting an ambiguity explicitly

When a question cannot be fully answered but doesn't block any specific phase, it must be explicitly accepted as ambiguity before Phase 6 closes — not silently carried. The format makes the assumption visible and auditable:

```markdown
[2026-05-29] ACCEPTED-AMBIGUITY: OQ-2 — aUSDC yield accrual timing
Assumption: Aave v3 accrues yield continuously via liquidity index — no discrete harvest window.
Why acceptable: Industry-standard behavior; verified in Aave v3 docs; MEV risk is nil for continuous accrual.
Risk if assumption is wrong: harvest() MEV vector would be underspecified in THREAT-MODEL.md — front-running window could exist.
```

The distinction between CLOSED and ACCEPTED-AMBIGUITY matters for auditors: CLOSED means the question was answered and a decision was made. ACCEPTED-AMBIGUITY means the question was acknowledged, an assumption was documented, and the risk of that assumption being wrong was explicitly evaluated.

---

## Closing an open question

```markdown
[2025-03-09] CLOSED: What happens to pending liquidations if oracle goes stale?
Decision: Block all liquidations when oracle is stale (revert with StalePriceData error).
Rationale: Allowing liquidations on stale prices creates an MEV window where an attacker
can time the staleness to force liquidations at favorable prices. The protocol's risk model
prefers positions staying open over being liquidated at wrong prices.
See: THREAT-MODEL.md — liquidate() oracle manipulation vector
```

---

## Logging a blocker

```markdown
## Blockers

[2025-03-11] BLOCKED: Audit firm availability
Blocking: Implementation start (want audit scheduled before code freeze)
Owner: Gil — checking availability with 3 firms
Target resolution: 2025-03-15
```

---

## Logging a deferred idea

Good ideas that are explicitly out of scope for v1 — not forgotten, but parked:

```markdown
## Deferred Ideas

[2025-03-09] DEFERRED: Protocol-owned liquidity bootstrapping mechanism
Why deferred: Adds significant complexity to the economic model; not needed for MVP launch.
Reconsider: After v1 launch, before v2 planning.
```

Deferred ideas are better than deleted ideas. They prevent re-litigating the same discussions in future sessions.

---

## Phase history

Update the phase checklist in STATE.md when a phase is complete:

```markdown
## Phase History

- [x] Phase 1 — Protocol Research — completed 2025-03-08
- [x] Phase 2 — Economic Design — completed 2025-03-09
- [ ] Phase 3 — Architecture & Contracts
- [ ] Phase 4 — Threat Modeling
- [ ] Phase 5 — Interface, Storage & Events
- [ ] Phase 6 — Test Specification
- [ ] Implementation
```
