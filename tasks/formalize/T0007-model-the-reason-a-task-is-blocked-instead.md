---
id: T0007
type: task
title: Model the reason a task is blocked, instead of discarding it on import
description: "The seven-value status enum keeps what state a task is in and throws away why, so an import had to invent a field mid-port to avoid losing it."
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

A real tracker's statuses carried the reason inside the status: *"Blocked —
handles and Page names frozen by Ashlee 2026-11-02 until the new site is
done"*. Mapping that to `Not Ready` keeps the state and discards everything
that makes it actionable. `imported_status:` was invented during the port to
avoid the loss, which is a workaround, not a model.

Two shapes worth weighing. A `status_note:` field is cheap and always
available. Representing the blocker as a node is truer but only works when the
blocker is someone's work — in the same import, one blocker was a discrete
task and the other was a client-imposed freeze tied to an untracked
milestone, and inventing a task for the second would have been worse than the
gap.

# Acceptance criteria

- [ ] A task can record why it is in its current state without that living only in prose
- [ ] `bin/status` surfaces the reason beside the status
- [ ] An external blocker that is nobody's task is representable without inventing one
- [ ] `imported_status` is either promoted to the model or retired

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-08 — decided, and no field was added

[D0008](../../knowledge-catalog/decisions/D0008-a-blocker-is-a-node.md): a
blocker is a link to a node, never free text. Another task, or a whole plan
resolved as "every task under that initiative is done", or — for something
external — the actionable task hiding inside it.

The `waiting_on:` field this task was written to specify was **rejected**. It
handles every case, which is the problem: *"Ashlee to decide the card handle"*
is unactionable, reaches no frontier, and is assigned to nobody. On the one
tracker with this shape, two of three external blockers decomposed into a
one-email task, and both appeared on the frontier the same afternoon. The
field records paralysis; the task gets it done.

`bin/status` resolves a plan target; a plan with no tasks is **not** done.
`imported_status:` stays a transitional note that promotion removes.
