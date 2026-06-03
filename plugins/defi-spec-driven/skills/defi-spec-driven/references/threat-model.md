# Phase 4 — Security & Threat Modeling Spec
> **Language rule**: Every file you write must be entirely in English — regardless of the conversation language.

**Goal**: Apply the security analysis systematically, function by function. Every external function gets a documented threat model entry.

Load: `PROTOCOL.md`, `STATE.md`, `economics/INVARIANTS.md`, `architecture/CONTRACTS.md`, `architecture/ACCESS-CONTROL.md`
Output: `threat-model/THREAT-MODEL.md`

---

## Core principle

This phase is about applying what was learned in Phases 1–3 systematically to each function — not about learning new security concepts. Phase 1 surfaced the relevant exploit patterns for your category. Phase 2 defined the economic invariants that must be protected. Phase 3 defined who can call what. Phase 4 connects all of that to each individual function's attack surface.

**Do not use a fixed checklist.** Think freely per function. The relevant attack vectors for a `deposit()` into a vault are not the same as the relevant vectors for a `liquidate()` in a lending protocol. Let the function's context — what it does, what state it touches, what value it handles, who can call it, what external calls it makes — drive the analysis. A checklist applied mechanically produces false confidence. Context-driven analysis produces real security.

---

## Workflow acceleration (optional)

Threat modeling is per-function and each function's analysis is independent — this is naturally parallel work. If multi-agent workflows are available in your Claude Code environment:

```
run a workflow to threat-model all external functions in [ContractName],
using INVARIANTS.md, CONTRACTS.md, and ACCESS-CONTROL.md as context,
producing a THREAT-MODEL.md with one entry per function
```

The workflow spawns one agent per function, all running concurrently. For a contract with 8 external functions, this compresses sequential analysis into one parallel pass. Each agent performs the same free-form analysis described below — no fixed checklist, context-driven per function.

After the workflow completes: review each entry before closing Phase 4. The workflow handles breadth; your review handles quality.

If workflows aren't available, work through each function sequentially using the format below.

---

## The per-function threat model entry

For every `external` and `public` function:

```
Function: [functionName(parameters)]
Contract: [ContractName]
Caller: [anyone / role / specific address]
Value at risk: [What can be stolen, corrupted, or locked if this function is exploited]

Attack vector 1: [Name or description]
  Mechanism: [How the attack works against this specific function]
  Conditions: [What has to be true for the attack to be feasible]
  Mitigation: [How the protocol prevents it or limits its impact]
  Residual risk: [Any remaining exposure and why it's acceptable]

Attack vector 2: [...]
  ...

Open questions: [Any unresolved security questions about this function]
→ Add to STATE.md
```

**What "value at risk" means**: Be specific. Not "user funds" — but "user's proportional share of vault assets, up to totalAssets." The specificity matters because it drives the severity assessment and the mitigation priority.

---

## How to use LLM in this phase

**Your analysis first.** Work through the function yourself using your knowledge of the protocol category and the post-mortems from Phase 1. Document the attack vectors you identified.

**Then LLM as second pass.** Describe the function to Claude: its name, parameters, what state it mutates, what external calls it makes, and what invariants it must preserve. Ask it to enumerate attack vectors it sees. Do not give it the vectors you already found — you want an independent perspective, not a confirmation.

This two-pass approach catches more than either alone. Your analysis has the protocol context the LLM lacks; the LLM has breadth across DeFi patterns that complements your focus.

**Treat LLM findings critically.** Not every vector the LLM names is applicable to your specific function. Some will be generic patterns that your design already addresses. Review each one against your actual code structure.

---

## Emergency mechanisms (document in this phase)

Every DeFi protocol should have a documented emergency plan. Answer these before Phase 5:

- Is there a pause function? Which specific functions does it pause?
- Who can trigger it, and under what conditions?
- What is the process for deploying a fix after a pause?
- **Can users still withdraw in a paused state?** (This is the critical question — a pause that traps user funds creates a new attack vector.)
- What is the communication plan if an exploit is detected?

---

## Populate THREAT-MODEL.md

```markdown
# Threat Model

## [ContractName]

### [functionName(params)]
**Caller**: [Public / Role: X / Owner]
**Value at risk**: [specific description]

**Attack vector: [name]**
- Mechanism: [how]
- Conditions: [when feasible]
- Mitigation: [CEI / nonReentrant / TWAP / etc.]
- Residual risk: [none / acceptable because X]

**Attack vector: [name]**
- [...]

---

### [nextFunction(params)]
[...]

## Emergency Plan

**Pause scope**: [which functions]
**Pause authority**: [who can trigger]
**User withdrawal during pause**: [yes/no — and if no, explicit justification]
**Recovery process**: [steps]
```

---

## Done when — gate

Before advancing, name the path of each file created this phase. If you cannot name it, it does not exist — create it now. Complete any unchecked item before loading the next phase reference.

- Every `external` and `public` function has a threat model entry
- Emergency mechanism is fully documented with explicit answer on user withdrawals during pause
- All open security questions are logged in `STATE.md`
- You have done both your own analysis AND a LLM second-pass for each function
- `STATE.md` Phase History updated: `[x] Phase 4 — Threat Modeling`

Transition: "Phase 4 complete. Say **interface spec** to begin Phase 5."
