# Session Handoff — Pause & Resume

## Pausing work

Before ending a session, update STATE.md with the current context so the next session can resume without reconstruction overhead:

```markdown
## Session Handoff

Last active: [date]
Active phase: [Phase N — Name]
Active contract (if in implementation): [ContractName]
Active function (if in execute loop): [functionName()]

### What was just completed
[1–2 sentences: what was done in this session]

### What to do next
[Specific next step — not "continue Phase 4" but "threat model entry for redeem() function"]

### Decisions pending
[Any decisions that were in flight but not recorded yet]
```

Update the phase history checkboxes in STATE.md.

---

## Resuming work

Load base context first: `PROTOCOL.md` + `STATE.md`.

Read the Session Handoff section in STATE.md to orient:
- What phase is active?
- What was the last thing completed?
- What is the immediate next step?

Then load the on-demand context for the active phase (the relevant phase reference file and output files). Do not load all output files — load only what's needed for the next step.

Confirm the current state with the user before proceeding:

> "Resuming [Protocol Name] — currently in [Phase N]. Last session completed [X]. Next step: [Y]. Shall I proceed?"

---

## What STATE.md must always contain at session end

- All decisions made (with rationale)
- All open questions (with blocking phase noted)
- Session Handoff block (what was done, what's next)
- Phase history updated

If STATE.md is not updated before ending a session, context is lost and the next session starts cold.
