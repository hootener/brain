---
id: D0007
type: decision
title: Every status transition has one named owner, and the brain is kept current in three layers
description: "A hook reconciles what is mechanically checkable, skills move status as a side effect of the work, and /audit handles only genuine ambiguity — stating what it checked before it asks."
timestamp: "2026-11-07"
tags: [brain, status, agents, audit]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "A status transition with no named owner does not happen, and no tool changes an inferred status without an answer."
constrains: ["bin/reconcile", "bin/status", "bin/lint", "skills/implement", "skills/spec-review", "skills/audit"]
---

# Context

Most work now arrives agentically, and the brain hears about it only when the
operator remembers to say so. Two kinds of drift are unowned:

- **Status.** `Testing → Complete` had **no owner anywhere in the system**,
  which is exactly why closing out an initiative was a thing the operator had
  to ask for.
- **Orphan work.** An agent told "fix the retry bug" fixes it, and no node
  records that it happened. Status drift makes the brain *wrong*; orphan work
  makes it *incomplete*, which is harder to notice because the graph still
  looks healthy.

# Decision

**Three layers, in descending reliability.**

**1. A hook, which cannot be forgotten.** Mechanical reconciliation only, no
model: a commit naming `T0005` means that task is not `Not Ready`; a claim
with no journal entry in an hour is stale; a merged branch whose task is
`Testing` is a candidate for `Complete`; a commit touching a `constrains:`
path while citing no decision is flagged. It **never guesses semantics** —
anything ambiguous is queued for layer 3, and that restraint is the entire
reason its output can be trusted unread. It does not mutate the index: it runs
`post-commit` and on Claude Code's `Stop`, where editing is safe and an agent
receives the output as feedback. A git hook is the floor, so humans and
non-Claude agents are covered; the `Stop` hook is the accelerator.

**2. Skills move status as a side effect of the work.** Each transition is
owned:

| Transition | Owner |
|---|---|
| `Not Ready → Ready for Development` | `/triage` |
| `Ready for Development → In Progress` | `/implement` |
| `In Progress → Testing` | `/implement` |
| `Testing → Complete` | `/spec-review` |
| `Complete → Delivered` | the hook |
| any `→ Superseded` | `/grill-change` or `/audit` |

Putting `Testing → Complete` on `/spec-review` is the keystone: it gives the
review a trigger it lacked and means a task cannot reach `Complete` without
the reverse walk having run.

**3. `/audit`, on demand, for ambiguity only.** It walks a plan's subgraph
forward and sorts every node into reconciled (silent), determinable (batched
into one confirmation) and undeterminable. On the last, **the question must
carry what was checked**: *"two of three criteria are verifiably met; the
third, failures surface in the operator log, I cannot verify — no test and no
log call"* is answerable in seconds, where *"I cannot tell if T0005 is
complete"* merely hands the work back. Same discipline as `/grill-change`
citing a path.

**An audit never silently changes a status it inferred.** Nothing moves
without an answer; unanswered questions are written to an Evidence node and
stay visibly open.

`/audit` also walks from the commits, to catch orphan work.

**`bin/reconcile` supplies the facts and `/audit` the judgement**, the same
split as `bin/relevant` and `/grill-change`.

**Cadence is by boundary, not by timer.** The hook and the skills cost the
operator nothing and run always. `/audit` runs when a plan's last task reaches
`Testing`, before a handoff, or on request; `bin/doctor` may warn that one is
overdue without running it.

**Retros are offered, never required.** A solo operator holds the context and
a retro is pure overhead; a team usually wants one. So the `Testing →
Complete` gate requires that **a review happened** — a `# Log` entry — with
the Evidence node as the optional richer form carrying `amends:`.

# Alternatives

- **An audit that runs on a timer** — rejected: constant questions train the
  operator to rubber-stamp, and a rubber-stamped brain is worse than a stale
  one because it looks maintained.
- **A hook that edits files during a commit** — rejected: mutating the index
  mid-commit is hostile.
- **A hook that infers semantics** — rejected: its value is that its output
  needs no review, and one wrong guess ends that.
- **Mandatory retro per task** — rejected: thirty Evidence nodes per
  initiative, most saying "no drift".
- **Asking the operator to close things out** — the status quo; it is the
  problem.

# Consequences

- `/audit` must be able to report "the brain is fine", and that must be the
  common outcome. An audit that always finds something means layers 1 and 2
  are broken, which is itself the signal.
- An audit's output is an Evidence node (`reviewed:`, `amends:`), so "when was
  this last audited" is answerable and feeds the overdue warning.
- `/implement` gains an enforced journal and the `Testing` handoff; it loses
  responsibility for `Complete`.
- `bin/status` grows `--owner` and `--mine`, which is also the team story.
