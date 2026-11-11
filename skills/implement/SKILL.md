---
name: implement
description: "Implement a piece of work described by a task or spec, after reading the intent that asked for it."
disable-model-invocation: true
---

Implement the work described by the user in the spec or task files under
`brain/tasks/`.

## Before any code — read what asked for this

```bash
brain/bin/ancestry <task id> --claims
```

This is not optional and it is not ceremony. **This is the only skill here that
writes code, and until recently it was the only one that never read a
decision.** It claimed the task, journalled, and started typing. Every
constraint the project had settled was one command away and nobody ran it.

Read the closure. If it prints `⚠ UNTERMINATED`, the task has no recorded
intent — say so to the user before building, because you are about to
implement something nobody can review against anything. If any ancestor is
flagged `⚠ SUPERSEDED`, build against the replacement the walk carried you to.

Then, for the files you expect to touch:

```bash
brain/bin/constraints <paths>
```

A decision that governs this change has to be honoured or amended. It does not
get silently contradicted — and if you find yourself about to, stop and say so
([D0005](../../knowledge-catalog/decisions/D0005-intent-layer-wiring.md)).

## Claim it, then work

Claim the task per
`brain/knowledge-catalog/reference/agent-skills/tracker.md`: set
`status: In Progress`, `claimed_by`, `claimed_at`, write, then **re-read to
confirm**. Claim first, work second.

Open your journal at `brain/work/<initiative>/<worker>.md` and **write to it as
you go**, not at the end. A claim with no entry in the last hour is treated as
stale and may be reclaimed by someone else, so an empty journal is not a tidy
habit deferred — it is your claim quietly expiring. Record what you tried, what
failed, and what the next session needs to know.

Do not start a task in a repository another worker is currently editing.

## While building

Use `/tdd` where possible, at the seams the spec already named. Run
typechecking and single test files regularly; the full suite once at the end.

**When you learn something the record does not know** — a gotcha, a
measurement, a reason the obvious approach does not work — write it down then,
as an `evidence` node (`brain/bin/new evidence "<what you found>"`) or as an
entry in `brain/knowledge-catalog/reference/E0001-brain-in-practice.md`. That
file is the first thing `/grill-change` reads, and it stays empty exactly when
work is going fastest.

## Finishing

1. `/code-review` for the artifact — correctness, security, performance,
   standards.
2. Commit to the current branch and push.
3. Set the task to `Testing`, record the PR URL in its `pr` field, and append a
   `# Log` entry saying what landed.

**Stop there.** `Testing → Complete` belongs to `/spec-review`
([D0007](../../knowledge-catalog/decisions/D0007-status-ownership.md)): a task
does not reach `Complete` without the reverse walk having run, because the
tests passing is evidence about the code and not about whether the right thing
was built.

**Workspace copy.** Adapted for this workspace: the issue tracker is `brain/tasks/` (local OKF task files), domain docs live in `brain/knowledge-catalog/`, and nothing agent-related is written into the repos. Read `brain/knowledge-catalog/reference/agent-skills/tracker.md` before publishing anything.
