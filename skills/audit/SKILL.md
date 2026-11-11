---
name: audit
description: Walk a plan, spec or goal's subgraph and reconcile every task's status against what the repository actually shows — fixing what is unambiguous, asking about what is not, and surfacing work that was done with no node at all. Use when an initiative is wrapping up, before a handoff, or whenever you want to know whether the brain still matches reality.
---

Most work arrives agentically now, and the brain hears about it only when
somebody remembers to say so. This is the layer that stops the remembering from
being the operator's job
([D0007](../../knowledge-catalog/decisions/D0007-status-ownership.md)).

It is the **third** layer, and it only handles what the first two cannot. The
hook reconciles what is mechanically checkable on every commit; the skills move
status as a side effect of doing the work. What reaches here is genuine
ambiguity — and if a lot reaches here, that is a signal those two are broken,
not that this one is working hard.

## Step 1 — get the facts

```bash
brain/bin/reconcile [initiative]
```

It compares every task's status against the history and sorts into three
buckets. **It never infers**, which is exactly why its output can be trusted:

- **reconciled** — status matches the evidence. Say nothing about these.
- **determinable** — wrong, and the evidence is unambiguous.
- **undeterminable** — honestly cannot tell.

Then walk the subgraph forwards, so omission shows up as well as drift:

```bash
brain/bin/ancestry <the plan, spec or goal> --forward
```

Anything marked `← outstanding` is either work remaining or a scope change
nobody recorded. A goal with four specs where three shipped and one was quietly
dropped is invisible any other way.

## Step 2 — batch the determinable

One confirmation for all of them, not one question each:

> T0005, T0009 and T0011 are all `In Progress`; their commits are merged and
> their acceptance criteria are met in the code. Set all three `Complete`?

## Step 3 — ask about the rest, and say what you checked

This is the part that matters, and the discipline is the same one
`/grill-change` follows in citing a path. **The question must carry what was
checked**, or you have handed the work back rather than done it.

Not this:

> I cannot tell if T0005 is complete. Is it?

This:

> **T0005 — Add retry to the webhook dispatcher.** Two of three acceptance
> criteria are verifiably met: the retry exists, and the backoff is covered by
> a test. The third — *failures surface in the operator log* — I cannot
> verify: there is no test for it and no logging call I can find on that path.
> Complete, or is that criterion outstanding?

The first takes four seconds of yours and four minutes of theirs. The second is
answerable immediately, and it is checkable — they can see what you looked at
and tell you that you looked in the wrong place.

**An audit never silently changes a status it inferred.** Nothing moves without
an answer. If the operator walks away, write the findings as an Evidence node
and leave the open questions visibly open — an unanswered question on the record
is worth more than a guess that reads as a fact.

## Step 4 — orphan work

```bash
brain/bin/reconcile    # its last section lists commits naming no task
```

An agent told "fix the retry bug" fixes it, and no node records that it
happened. **Status drift makes the brain wrong; orphan work makes it
incomplete**, which is harder to notice because the graph still looks healthy.

Group the commits and ask:

> These seven commits map to no task. Was this T0005, or is it unrecorded work
> that needs a node?

Often the answer is a one-line task written retroactively, which is fine — the
record is for the next reader, not for the ritual.

## Step 5 — record the audit

Write an Evidence node: `brain/bin/new evidence "Audit of <initiative>, <date>"`,
with `reviewed:` naming what it covered and `amends:` naming any node whose
status or claims it corrected.

That makes "when was this last audited" answerable, which is what lets
`bin/doctor` warn that one is overdue without running one.

## Cadence

**At boundaries, never on a timer.** When a plan's last task reaches `Testing`,
before a handoff, or when asked. An audit that runs constantly and asks
questions trains the operator to rubber-stamp, and a rubber-stamped brain is
worse than a stale one because it *looks* maintained.

## Reporting that nothing is wrong

Do it plainly, and expect it to be the common outcome. If the hook and the
skills are doing their jobs, most audits find nothing. **An audit that always
finds something means layers one and two are broken** — say that, because it is
more useful than the findings.
