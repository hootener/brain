---
id: D0008
type: decision
title: "A blocker is a node, not a sentence"
description: "What blocks a task is another task or a whole plan, named as a link; an external blocker is decomposed into the actionable task hiding inside it rather than recorded as prose."
timestamp: "2026-11-08"
tags: [brain, tasks, blocking]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "A task's blocker is a link to a node, never free text."
constrains: ["bin/status", "bin/lint", "skills/triage", "skills/implement", "tasks/"]
---

# Context

Two real trackers wrote the reason into the status itself:

> `status: Blocked — handles and Page names frozen by Ashlee 2026-11-02 until
> the new site is done; card handle decision hers`

Mapping that to `Not Ready` keeps the state and discards everything that makes
it actionable, so an `imported_status:` field was invented mid-port to avoid
losing it. That is a workaround, not a model.

# Decision

**A blocker is a link to a node.** Three cases and no fourth:

1. **Another task** — `blocked_by: [./T0007-….md]`, as today.
2. **A whole initiative** — `blocked_by: [../../plans/P0003-….md]`. A plan
   target is resolved as *every task under that plan's initiative is done*. A
   plan with no tasks is **not** done: nothing started is not the same as
   nothing left.
3. **Something external** — find the actionable task inside it and block on
   that. There is almost always one, and it is usually an email.

**A reason that is neither is not a blocker.** A date you are waiting for is a
constraint; a fact you noticed is a note. Neither belongs in `blocked_by` and
neither should be dressed as one.

# Alternatives

- **A `waiting_on:` free-text field** — rejected, and this was the close call.
  It handles every case, which is exactly the problem: *"Ashlee to decide the
  card handle"* is unactionable, never reaches the frontier, and is assigned to
  nobody. **The field lets you record paralysis; the task makes it work.**
  Measured on the one tracker that had this shape, two of three external
  blockers decomposed into a one-email task, and naming them changed the
  outcome — both appeared on the frontier the same afternoon.
- **`blocked_by` accepting arbitrary strings** — rejected: it breaks the
  frontier computation, which resolves every target and treats an unresolved
  one as open.
- **A new `blocker` node type** — rejected: a thing somebody must do is a task.
  It needs no second name.

# Consequences

- `bin/status` resolves a plan target by looking at its initiative's tasks.
- `/triage` and `/implement` push for the actionable task when a status carries
  a reason in prose.
- `imported_status:` remains a transitional field `INIT.md` writes and
  promotion removes. It is a note, never a model.
- A task blocked on something genuinely outside anyone's control keeps an empty
  `blocked_by` and says so in its `# Log`. That is honest and rare.
