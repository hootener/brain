---
id: D0006
type: decision
title: Code review judges the artifact, spec review judges the intent
description: "code-review keeps correctness, security, performance and standards and becomes brain-free; spec-review owns the whole reverse walk including the spec axis, and the duplicated Intent axis is deleted."
timestamp: "2026-11-07"
tags: [brain, review, skills]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
constrains: ["skills/code-review", "skills/spec-review", "bin/ancestry"]
---

# Context

`skills/code-review` was adapted from an upstream skill with **two** axes,
Standards and Spec. An Intent axis was grafted on as a third and the graft
never finished: the description says three, the step-4 heading still says
"Spawn both sub-agents", and the Intent axis calls `brain/bin/ancestors` —
a command that does not exist — while claiming to follow `intent` and
`handoff` edges, which are not types. It has never been able to run.

So the reverse walk had two implementations and the one inside `code-review`
was the broken one.

# Decision

Two skills, disjoint inputs, run at different moments.

**`/code-review` judges the artifact.** Correctness, security, performance,
Fowler smells, documented repo standards. It reads the diff and the standards
files, and touches the brain for exactly one thing: the glossary, so names
match the domain vocabulary. Otherwise brain-free — which makes it portable to
a project with no brain, and replaceable by a team that already has its own.

**`/spec-review` judges the intent**, and owns the reverse walk entirely,
**including the spec axis**. "Did the diff implement the spec" and "does it
still serve the intent" are the same question at two altitudes and they share
one walk; splitting them would have two skills reading the spec.

The Intent axis is deleted from `/code-review` rather than fixed.

Preserved from the old design: parallel sub-agents so axes do not pollute each
other's context, and **no reranking across axes** — the reasoning that a
Standards pass can mask a Spec fail still holds among code-review's own axes.

The orphaned "initiative-level intent review before a handoff" moves to
`/spec-review` as the forward walk at initiative scope.

# Alternatives

- **Fix the Intent axis in place** — rejected: it would be a second
  implementation of `/spec-review`'s walk, kept in sync by hope, under a
  400-word cap on what `/spec-review` calls the deliverable.
- **Spec axis stays in `/code-review`** — rejected: both skills would then
  read the spec, and the spec axis is the shallow end of the same walk.
- **One review skill with four axes** — rejected: the two halves want
  different triggers. Code review runs while coding; spec review runs once,
  before `Complete`.

# Consequences

- `/code-review` loses its dependency on `bin/ancestry` and on the brain's
  structure, and can be dropped into a repository without one.
- `/spec-review` becomes the gate on `Testing → Complete` (see
  [D0007](D0007-status-ownership.md)).
- `bin/ancestors` is not created; the name in the old skill was always a typo
  for `bin/ancestry`.
