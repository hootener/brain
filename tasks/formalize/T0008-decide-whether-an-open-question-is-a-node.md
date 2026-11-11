---
id: T0008
type: task
title: Decide whether an open question is a node type
description: "Sixty-nine tracked open questions collapsed into one 1,119-line evidence node because the taxonomy has nowhere else to put them."
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

An engagement tracked **69 open questions**, each with an owner, a date and
often a ⚠ severity. The brain has no node for *"an unresolved question"*:
it is not a task (no acceptance criteria, often not even our work), not a
decision (undecided by definition), and only barely evidence.

They became a single evidence node of 1,119 lines — findable by
`bin/relevant`, but unsortable, unassignable, and invisible to `bin/status`.

This is a real taxonomy question, not a defect, and the answer may well be
**no**: an eighth type is a cost, and `wayfinder` already models a question
whose resolution is a decision. But the current answer — *"put it in a
1,100-line evidence file"* — is not one anybody chose.

# Acceptance criteria

- [ ] A decision is recorded either way, with the alternatives weighed
- [ ] If yes: the type, its body shape, and how `bin/status` treats it
- [ ] If no: where 69 owned, dated open questions are supposed to live, stated explicitly

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-08 — decided: no new type

[D0009](../../knowledge-catalog/decisions/D0009-an-open-question-is-a-task.md):
a question that blocks work is a `task` with `category: question`; one that
blocks nothing stays evidence.

The deciding evidence was in the corpus that raised the question. Across its
69 open questions there were **84 `Owner:` annotations** — nearly every one
already named who had to act, which is the definition of work. `category` is
an existing enum, so this adds a value rather than a type.

Migration is gradual: a question is promoted when it starts blocking
something. Converting all 69 would have produced dozens of rows for questions
nobody will action.
