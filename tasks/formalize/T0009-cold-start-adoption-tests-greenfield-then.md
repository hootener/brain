---
id: T0009
type: task
title: "Cold-start adoption tests: greenfield, then the product team"
description: "Have an agent that did not build this brain adopt it, twice: once into an empty project and once into a project with four years of its own conventions."
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-08"
tags: [brain, formalize, adoption]
---

# What to build

Not a build. Two experiments, run by a Claude session that did **not** write
this brain, because the author is the worst available tester — the template
passes its own linter by construction, and the same blindness applies to the
person driving the port.

[T0005](T0005-add-bin-selftest-adopt-into-a-throwaway-host.md) covers the
mechanical install. What neither it nor anybody else has tested is the
**onboarding path a human and a cold agent actually walk**: README →
`bin/adopt` → `CLAUDE.md` → `INIT.md`.

**Scenario A — greenfield.** A brand-new, empty project. `INIT.md` has
nothing to import and nothing to mine, so the first goal is its only real
output. Unknown whether it degrades gracefully or reads as broken.

**Scenario B — a project with its own conventions.** Four initiatives, seven
decisions, fifty-seven tasks, two node types this brain deliberately does not
have (`intent`, `handoff`), two it deleted (`context`,
`initiative-pointer`), and **no version control at all**, so the
date-recovery advice in `INIT.md` is unavailable.

The deliverable is the friction, not the ported brain. Whatever the cold
session finds confusing, missing or wrong goes to
[E0001](../../knowledge-catalog/reference/E0001-brain-in-practice.md) and
becomes tasks from there.

# Acceptance criteria

- [x] Scenario A run by a session with no prior context; its transcript audited
- [x] Scenario B run the same way
- [x] Every point of friction recorded in E0001, separated into documentation
      gaps and real defects
- [~] Anything that turned out to be a defect has a task — **not met as
      written**; see the closing log entry

# Out of scope

- Doing either port myself. That would answer a different question.

# Log

## 2026-11-08 — Scenario A under way

Predictions recorded before the run, so they can be scored rather than
reconstructed afterwards:

1. `bin/adopt` refuses on a project not yet `git init`ed, which a brand-new
   folder usually is not. The message points at `git init` or `--sibling`.
2. `INIT.md` steps 2-4 — import ADRs, seed the glossary, mine the
   transcript history — all come up empty on a new project. Untested whether
   that reads as graceful or as broken.
3. The first goal is the only substantive output. If the session does not push
   for one, the brain starts with an unterminated lineage and no tool will say
   so until something is built.
4. `example/` may be mistaken for the adopter's own content rather than a
   worked example.

## 2026-11-09 — closed

Both scenarios ran, five rounds each. Scenario A was `the solo research brain`, an empty
non-git project; Scenario B was `the product team's repository`, which migrated 109
nodes and then stayed in the loop through five update rounds. Findings live in
[E0003](../../knowledge-catalog/reference/E0003-adoption-greenfield.md) for A
and in rounds 1-10 of
[E0001](../../knowledge-catalog/reference/E0001-brain-in-practice.md) for both.

**The fourth acceptance criterion was not met as written, and the deviation was
deliberate.** "Anything that turned out to be a defect has a task" would have
produced about twenty tasks describing work already done — the fix usually
landed in the same session as the report. What they got instead was a named
check in `bin/selftest`, one per finding, which is the discipline `howto.md`
now states as *a fix lands with its check*. 86 checks, from 43 when this task
opened. A task per defect would have been a worse record: a backlog of closed
items instead of a suite that fails if the defect returns.

**The predictions, scored.** They were written down so they could be scored
rather than reconstructed, so:

1. **Wrong in form, right in area.** `bin/adopt` did not refuse on a non-git
   project; the friction was that the README's own quickstart omitted
   `git init` (E0003 finding 7). The guess named the right hazard and the wrong
   mechanism.
2. **Right, and it mattered more than expected.** The greenfield branch read as
   broken rather than graceful — `INIT.md` now branches on greenfield before
   step 1, and the greenfield branch mines the live conversation instead
   (finding 5).
3. **Right.** The first goal was the substantive output, and nothing pushed for
   one. `serves:` on Approach nodes and `ancestry --claims` reporting
   `⚠ UNTERMINATED` came out of this.
4. **Wrong as stated, and the real problem was worse.** `example/` was not
   mistaken for the adopter's content; it silently reserved ids, so a default
   install issued `D0010` as its first decision (finding 3). Being *mistaken*
   for content would have been visible. Reserving ids was not.

Three of four predictions pointed at the right area and two got the mechanism
wrong, which is roughly the hit rate the task existed to establish: the author
can guess where a cold adopter will trip and not how.

**What this task does not close.** Both adoptions ran with the author on the
other end fixing defects within the hour. **Nobody has adopted this without
the author in the loop**, which is the test that would actually establish
portability, and it is not something this task can run. The tooling is now
clean enough that the attempt should survive — `bin/prove` exists so a stranger
can catch the author's claims without asking — but that is a prediction, not a
measurement.

The adopter's own recommendation was to stop the loop: severity fell about an
order of magnitude per round, and by round 10 they were producing false
findings at roughly the rate of real ones. Closed on that recommendation.
