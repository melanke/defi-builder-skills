# Quick Mode

For small, self-contained tasks that don't warrant the full 6-phase pipeline. Bug fixes, config changes, parameter updates, isolated function additions to an already-specified contract.

---

## When quick mode applies

- ≤ 3 files affected
- The scope fits in one sentence
- No new invariants introduced
- No new threat surface introduced
- The change is consistent with an already-complete spec

If any of these don't hold, quick mode doesn't apply — use the full pipeline or the relevant phase.

---

## Quick mode flow

1. **State the task** in one sentence
2. **Check the spec** — does this change affect any REQ-* or INV-*? If yes, update the spec entry first
3. **Implement** the change
4. **Gate check**: `forge fmt --check` + `forge build` + `forge test`
5. **Mark in IMPL-TASKS.md** if the change relates to a tracked function
6. **Commit** with a clear message referencing any affected slug

---

## What quick mode is not

Quick mode is not an escape hatch from spec discipline. A "small" change that introduces a new state mutation to an existing function is not quick mode — it requires updating the REQ-* entry and re-running the relevant threat model analysis.

When in doubt, spend 5 minutes checking: does this change add any attack surface, modify any invariant, or change any documented revert condition? If yes, update the spec. The spec is living documentation, not a frozen artifact.
