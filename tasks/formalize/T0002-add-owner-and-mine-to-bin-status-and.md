---
id: T0002
type: task
title: Add --owner and --mine to bin/status, and owner inheritance from plan to spec
description: "Add --owner and --mine to bin/status, and owner inheritance from plan to spec"
status: Not Ready
wontfix: true
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

Ownership attaches at Approach and above and is inherited downward; a task's
owner is its plan's owner unless overridden. Claims already cover *who is on it
now*, so no `assigned_to` field is added — a field duplicating a claim will
drift.

Must not make solo use heavier: with no `owner:` anywhere, every flag is a
no-op and the output is unchanged.

# Acceptance criteria

- [ ] `bin/status --owner <name>` filters to nodes owned by that team or person
- [ ] `bin/status --mine` resolves the current worker name the way a claim does
- [ ] An owner is inherited from the task's spec, and from that spec's plan, unless set locally
- [ ] With no `owner:` set anywhere, output is byte-identical to today's

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-11 — wontfix, on measurement rather than taste

Not built, and the reason is recorded here rather than argued three more times
in conversation.

**The premise did not survive contact with the corpora.** Ownership is only
worth modelling where more than one person competes for work, and across five
brains:

* the consulting adopter carried `owner:` on six tasks, every one of them the same person, and
  **dropped the field** on the test an adopter proposed — *does it change how
  a conflicting record is read?* One name on six tasks arbitrates nothing.
* the product team's repository is the only multi-person corpus and never asked for
  this in ten rounds of feedback, with 63 tasks and 30 plans.
* the solo research brain, the uncoached adopter and this brain are single-operator.

So the feature would be built for a team that exists, has the most tasks of
anyone, and has not wanted it. `claimed_by` already answers *who is on this
now*, which is the question that actually gets asked.

**What would reopen it:** a brain where two people are visibly picking from one
frontier and `bin/status` is making that harder. That is a complaint somebody
makes, not a gap to anticipate — and this record's own evidence is that the
tools added on anticipation (`stale`, `reconcile`, `ancestry` in some corpora)
are the ones nobody reaches for.

`wontfix: true` rather than `Superseded`, because nothing replaced it: the need
was never demonstrated. `bin/status` drops it from the frontier, so no agent
picks up work three conversations have decided against.
