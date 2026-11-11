---
id: T0011
type: task
title: "Decide what the brain owes an artifact outside the repository"
description: "A finding was captured correctly and still failed to land, because the thing it needed to change lives outside the repo where no brain tool reaches."
status: Complete
category: question
initiative: formalize
serves: ../../knowledge-catalog/decisions/D0012-capture-is-separate-from-classification-and-capture-wins.md
blocked_by: []
timestamp: "2026-11-10"
tags: [brain, formalize, boundaries]
---

# What to build

A decision, not code — the question first.

An adopter went looking for a finding they believed had been lost to missing
capture. It had been captured: a task log entry, written minutes after the run
it described. What failed was downstream. The finding needed to change a voice
profile at `~/.claude/voice/profile/professional.md`, outside the repository,
and **no tool here crosses that boundary** — not `bin/note`, not `bin/new`, not
`bin/lint`, not `bin/stale`. The record was complete and correct and the thing
it existed to change went on being wrong for eight hours.

This is the same shape as the defect class the brain already has an instrument
for. `bin/unread` catches a field the record carries that no tool reads.
`bin/stale` catches a countable claim that no longer matches the files. Neither
catches **a claim whose subject is not in the repository at all**, and that
subject is where a lot of agent configuration now lives: skills, profiles,
settings, prompts under a home directory.

The question to settle, in order:

1. **Is a cross-boundary target in scope at all?** The honest alternative is
   that the brain describes one repository and anything outside it is the
   operator's problem — defensible, and it should be written down rather than
   left as an accident of what the tools happen to walk.
2. **If it is in scope, what is the mechanism?** A node naming an external
   path in a field a tool reads is the obvious shape, and the obvious hazard
   is that nothing can verify what it says about a file it cannot diff. A
   check that cannot fail is worse than no check — this record has paid for
   that lesson more than once.
3. **What does it cost to be wrong?** An external target that has silently
   drifted is exactly the stale-but-confident note D0001 exists to prevent,
   and it is less visible than any in-repo version of the same thing.

# Acceptance criteria

- [x] A decision node states whether artifacts outside the repository are in
      scope, with the reasoning and the rejected alternative — D0014
- [x] If in scope, the mechanism names which tool reads the field, and what
      that tool can and cannot verify about a path it does not own —
      `resource:`, read by `lint`, `ancestry` and `graph-build`, verifying
      existence and nothing else
- [x] If out of scope, `CLAUDE.md` says so where a reader will meet it, so the
      gap is a stated boundary rather than a thing nobody thought about

# Out of scope

- Building the mechanism before the decision is made. The last three tools
  added here were right because somebody measured the cost first.

# Log

## 2026-11-10 — opened

From the solo research brain's note round. Their framing, which is the reason this is a
task rather than a line in E0001: *"capture wasn't the failure"* — and the
cross-boundary gap *"is a better target than the backlog ever would have
been."*

## 2026-11-11 — closed as D0014

The question turned out to be narrower than it was written, because part of the
boundary was already crossed and working: `resource:` names an artifact outside
the repository, `bin/lint` errors when it does not exist and is silent when it
does, and two adopters use it today. Verified rather than assumed, on a fixture
with a real file outside the brain and then a missing one.

So the question was never whether the record can point outside itself. It was
**what the record is entitled to claim about what it points at**, and the
answer is: that the file is there, and nothing more.

The tripwire was rejected on maintenance rather than on principle. It is
genuinely verifiable and it can fail, which this record rates highly — but the
hash is stamped by hand, so a deliberate edit to the external file means
re-stamping or a warning that fires forever. That is the permanently-red
pattern already paid for twice here. The condition for revisiting it is
written into D0014: a brain where external artifacts are the subject rather
than an edge case.

The honest residue, stated in the decision rather than implied: **this makes
the gap legible, it does not close it.** A correctly captured finding whose
target lives outside the repo can still fail to land. What the eight hours
actually cost was nobody having said the gap existed.
