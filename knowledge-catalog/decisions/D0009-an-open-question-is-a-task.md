---
id: D0009
type: decision
title: "An open question that blocks work is a task; one that blocks nothing is a note"
description: "No node type for an unresolved question: it carries an owner and an action, which makes it a task with category question, and the rest stay evidence."
timestamp: "2026-11-08"
tags: [brain, taxonomy, tasks]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "An unresolved question is represented as a task or as evidence, never as a type of its own."
constrains: ["bin/lint", "knowledge-catalog/", "tasks/", "skills/triage"]
---

# Context

One engagement tracked **69 open questions** in a single 1,119-line document.
They were findable by search and nothing else: unsortable, unassignable, and
invisible to `bin/status`. The brain had nowhere else to put them, so the
answer it gave was *"a very long file"* — an answer nobody chose.

# Decision

**No new type.** An open question is one of two things already modelled:

- **It blocks work** → a `task` with `category: question`. `category` is an
  existing enum (`bug`, `enhancement`); this adds a value, not a type.
- **It blocks nothing** → it stays `evidence`, found by `bin/relevant`.

The deciding evidence is that the questions already were tasks. Across those
69 items there were **84 `Owner:` annotations** — nearly every one already
named who had to act, which is the definition of work.

Migration is **gradual and on demand**: a question is promoted when it starts
blocking something, and the bulk stays in the evidence node. A one-time
conversion would produce dozens of rows for questions nobody will ever action.

# Alternatives

- **An `open-question` type** — rejected under the rule that a type must fail
  to be expressible on an existing one. This is expressible: a question with an
  owner and an action is a task; a question with neither is a note.
- **Convert all 69 at once** — rejected: a day of work, most of it on items
  that will never be actioned, and a task table nobody can read.
- **Leave them as one evidence node** — rejected as the status quo. It is why
  a blocker sitting under two deadlines went unnoticed on both the client's
  tracker and the agent's.

# Consequences

- `category: question` joins `bug` and `enhancement`.
- Such a task is `Not Ready` until it is actionable, so it never crowds the
  frontier. `bin/status <initiative>` keeps the table readable.
- A question promoted to a task is the thing a later task names in
  `blocked_by`, which is how [D0008](D0008-a-blocker-is-a-node.md) resolves an
  external blocker.
