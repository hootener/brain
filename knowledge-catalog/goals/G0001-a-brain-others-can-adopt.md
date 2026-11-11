---
id: G0001
type: goal
title: A brain someone else can adopt without being told how
description: "An operator who has never seen this repository can fork it, install it into a project they are already halfway through, and have the record stay true without maintaining it by hand."
timestamp: "2026-11-07"
tags: [brain, onboarding, portability]
status: active
---

# Goal

Someone who is not the author can take this repository, point it at a project
that is **already in flight**, and end up with a record that stays true as the
work proceeds — without being taught the system first, and without a ritual
they have to remember to perform.

Three audiences, in order of how hard they are to serve:

1. **The author, on a new project.** Already satisfied by the template.
2. **A colleague who was handed the link.** Needs the model explained once, in
   the README, and an install that is one command.
3. **An agent working unattended inside someone else's project.** Needs the
   model in `CLAUDE.md`, the reflexes stated as obligations, and transitions
   that happen as a side effect of the work rather than because somebody
   remembered.

# Why

The template works for the person who built it, which is the weakest possible
evidence that it works. Everything that is currently carried in the author's
head — which skill to reach for, where a node goes, when to update a status —
is a thing a second operator does not have and an agent cannot infer.

The failure this guards against is specific and it is not "nobody adopts it".
It is **adoption followed by quiet rot**: the brain gets installed, a dozen
nodes get written, the statuses stop matching reality, and six weeks later it
is a graveyard that still looks maintained. A record nobody trusts is worse
than no record, because it is read.

# Signals

- A fork, a clone, `bin/adopt` and `INIT.md` produce a usable brain in a
  project with existing code, existing docs and no prior brain.
- `bin/doctor` passes on that install without the operator editing anything by
  hand.
- An agent given only `CLAUDE.md` places a new node in the right category and
  reaches for the right skill, without the operator naming either. **Split,
  because one half is measurable and the other is not:**
  - *Mechanical:* every field a tool reads is named in `CLAUDE.md`. An agent
    cannot use what it has not been told exists, and three corpora show the
    consequence — invented fields (`supersedes`, `intent:`, `owner`) and a
    decision layer that no tool could surface because `constrains:` was never
    mentioned. Checked by `bin/selftest`.
  - *Judgement:* whether the placement is actually right. No check can know
    this, and the only instrument is reading what an agent did in a session
    nobody coached. Measured three times so far, all three mis-sorting
    something — the commonest being a choice that arrived as prose from
    outside, filed as evidence rather than as a decision.
- Task statuses match reality without the operator having asked for them to be
  updated.
- `bin/lint` and `bin/stale` are green on a brain that has been in use for a
  month.
