---
id: P0001
type: plan
title: Ready the brain for other people to adopt
description: "Close the gaps the first two real adoptions exposed, so a second operator can install this brain into a project already in flight and trust what it tells them."
timestamp: "2026-11-08"
tags: [brain, formalize]
status: active
initiative: formalize
serves: ../../knowledge-catalog/goals/G0001-a-brain-others-can-adopt.md
---

# Intent (forwardable)

This repository is a system for keeping the reasoning behind a project — the
goals, the decisions, the plans, what was learned afterwards — as plain files
that a person, a diff and an agent can all read. It works for the person who
built it. This work is about making it work for somebody else.

Two real projects have now adopted it. Between them they surfaced a dozen
defects, none of which the system's own checks could have found, because a
system that checks itself passes by construction. What is left is the
difference between a tool that works in the hands that made it and one that
survives being handed over.

When this is done, someone who has never seen the repository can fork it,
install it into a project they are already halfway through, and have the
record stay true as they work — without being taught the system first.

# Goal

Close the gaps between "works for its author" and "survives being handed to
somebody else", as recorded in
[E0001](../../knowledge-catalog/reference/E0001-brain-in-practice.md).

# Approach

Fix what real adoption surfaces, in the order it surfaces it, and write each
finding down before fixing it so the fix can be checked against the claim. The
adoptions are the test suite until `bin/selftest` exists.

Three classes of work, which is why the tasks are not strictly sequential:

- **Tooling the adopter meets on day one** — the install, the checks, the
  defaults. A fresh install that reports seventeen warnings is a broken
  product regardless of what it does afterwards.
- **The model** — where things the real world has, and this brain does not,
  belong: a reason a task is blocked, an open question, an outward-facing
  handoff.
- **The record's own honesty** — the checks that stop a claim rotting, and the
  false positives that make people stop reading them.

# Sequence

1. **Adopt into real projects and fix what breaks.** Done twice; the findings
   are [E0001](../../knowledge-catalog/reference/E0001-brain-in-practice.md).
2. **Make a fresh install clean** — zero errors, zero warnings, ids starting at
   `D0001`. Done.
3. **Close the measurement gaps** — citations that no tool could see, counts
   that scope themselves. Done ([T0006](../../tasks/formalize/T0006-teach-lint-to-check-backticked-prose-paths-not.md)).
4. **Model what adoption showed missing** — the reason a task is blocked
   ([T0007](../../tasks/formalize/T0007-model-the-reason-a-task-is-blocked-instead.md)),
   whether an open question is a node
   ([T0008](../../tasks/formalize/T0008-decide-whether-an-open-question-is-a-node.md)).
5. **Stop relying on adoption as the test suite**
   ([T0005](../../tasks/formalize/T0005-add-bin-selftest-adopt-into-a-throwaway-host.md)).

# Risks

- **The brain grows a feature per adoption.** Every project will want
  something. The guard is that a new type must fail to be expressible as edges
  on an existing one before it is added — the test that kept `intent` and
  `handoff` out of the taxonomy.
- **Checks that cry wolf get switched off, and take the real findings with
  them.** Two heuristics have already produced more false positives than
  findings on real prose. Measure a rule against a corpus nobody wrote for it
  before shipping it.
- **The author is the worst tester.** Mitigated only by adopting into projects
  whose content was written for something else.

# Out of scope

- A hosted service, a database, or any runtime. The files are the product.
- Deadlines, consent dates, release processes, and anything else that models a
  particular team's way of working rather than the work.
