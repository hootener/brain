---
id: D0001
type: principle
title: The record is only worth keeping if its claims are checkable
description: "Every assertion in the brain must be traceable to something a reader can verify — a path, a measurement, a commit — or it does not belong in the record."
timestamp: "2026-11-07"
tags: [brain, meta, principle]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
---

# Principle

Nothing in this brain asserts something a reader cannot check. A claim carries
the path, the measurement or the commit that establishes it, or it is marked as
an opinion, or it is not written.

This is the constraint the rest of the system is built to satisfy, and it is
the reason to prefer a smaller true record over a larger plausible one.

# Rationale

A model asked to "consider the existing decisions" will produce something
decision-shaped whether or not it read any. A person writing a summary will
round a number toward the story. Neither is dishonesty; both are what happens
when nothing forces the claim to touch ground.

The cost is asymmetric and that asymmetry is the whole argument. An absent
note costs a reader nothing — they go and look. **A confident, stale note
costs them the thing they would have found by looking**, because they stop
looking. So the record's value is not how much it holds; it is the probability
that what it holds is true. Every feature in this repository is downstream of
protecting that probability.

# Implications

- A challenge cites a path. `/grill-change` may not push back without naming
  the file, and an uncited pushback must be labelled an opinion.
- Anything measured records the measurement, its denominator and its date, so
  a later reader can re-run it rather than believe it.
- Generated claims are checked by a tool, not by vigilance: `bin/lint` for
  conformance, `bin/stale` for countable claims, `bin/reconcile` for status
  against the repository.
- Supersede, never delete. A decision quietly edited to match what got built
  teaches the reader that the record is written afterwards, which destroys the
  probability this principle protects.
- A thin or unterminated lineage is **reported**, not padded. "We did not
  decide that" and "this work has no recorded intent" are findings, and both
  are more useful than a plausible reconstruction.
- An agent that cannot determine something asks. `/audit` states what it
  checked before it asks, so the question is answerable rather than delegated.
