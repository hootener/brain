---
name: spec-review
description: Review finished work against the intent that asked for it — walking the graph backwards to check the change still serves its goal and decisions, and forwards to check nothing the intent asked for was quietly dropped. Use after the code is written and the tests pass, before a task moves to Complete.
---

The code is written and the tests are green. A code review will now check the
diff against its acceptance criteria and find nothing wrong — which is the
failure mode this exists for, because **a diff can satisfy every criterion and
still have drifted from what was actually wanted.**

The criteria are the last link in a chain. A task is cut from a spec, the spec
serves a goal, and decisions constrain the route. Reviewing only the last link
reviews only whether somebody did what the ticket said.

This skill owns the whole reverse walk, **including whether the diff implements
the spec**. `/code-review` judges the artifact — correctness, security,
performance, standards — and does not read the graph
([D0006](../../knowledge-catalog/decisions/D0006-review-split.md)).

## Step 1 — walk backwards, and check the walk terminated

```bash
brain/bin/ancestry <id(s)> --claims
```

It follows **lineage** (`spec`, `serves`, `plan`, body links), follows
**corrections** (`superseded_by`, `amends`) *even though they point forward*,
and does **not** follow `blocked_by` — a blocker is scheduling, not an
ancestor. Output is grouped by the four categories, each with its own question.

**Read the termination line first.** Every branch must end at a `goal` or a
`principle`. If the walk prints `⚠ UNTERMINATED`, stop and report that before
anything else:

> This work has no recorded intent. The spec names no `serves:`, so there is
> nothing to check the change against. The review cannot be completed as a
> review of intent until that is fixed.

That is a finding about the record, not a reason to review more quietly. A thin
closure and a complete one used to look identical; now they do not, so do not
make them identical again by padding.

**Watch for `⚠ SUPERSEDED`** on any ancestor. Reviewing a change against a
decision that has been replaced is the worst outcome this skill has. The walk
carries you to the replacement; review against that.

## Step 2 — does the change still serve the why?

For each ancestor, hold its **promise** — the `description` is literally the
one-line promise the document makes — against what was built. You are looking
for drift, not violation: a violation shows up in tests, and drift does not.

> The spec says PDF or DOCX. The plan widened it to `.doc`. The code
> implements the plan and passes. Nobody went back to the spec sentence.

Nothing there is wrong, exactly. The spec is now quietly untrue, the widening
was never argued, and the person who wrote the spec does not know.

Ask, per category:

- **Intent** — is every claim still true, and does the change still serve it?
  A decision made for a reason that no longer holds is not wrong, it is
  unexamined, which is worse.
- **Approach** — what did the nearest spec or plan promise that the code does
  not do? What does the code do that no ancestor asked for? Scope that arrived
  during the build is the commonest drift and the easiest to miss, because
  everything about it looks deliberate.
- **Evidence** — does anything measured contradict the Intent above it?

## Step 3 — walk forwards: was anything quietly dropped?

```bash
brain/bin/ancestry <the goal or spec the walk terminated at> --forward
```

The backward walk answers *why was this built*. It cannot answer the other half
of **did we do what we expected**: what the intent asked for and nobody built.
A goal with four specs where three shipped and one was dropped is invisible
backwards, and so is a spec whose last two tasks sat at `Not Ready` while the
initiative was called done.

That is **omission rather than drift**, and it is the failure a green test
suite hides best. Anything the forward walk flags `← outstanding` is either
work remaining or a scope change nobody recorded. Say which.

At initiative scope this is the whole review: run it before any handoff.

## Step 4 — what did the build make false?

`--claims` pulls the sentences each ancestor asserts. After the work, some are
false. **Finding them is half the review, and the output is a list of documents
to amend.**

> A decision said the review step was triggered by a webhook and reported
> through one API. The ticket built a dispatch-and-poll loop and a different
> status mechanism — for good reasons — and nobody walked back up for two days.
> A reader of the decision record was being told about an integration nobody
> had ever built.

For each ancestor: **is every claim in here still true?** Watch for

- a mechanism described that was built differently,
- a count that has moved (`brain/bin/stale` catches the countable ones; nothing
  catches a mechanism except reading),
- a **named exclusion** that has since been built — most often missed, because
  nobody re-reads a list of things they decided not to do,
- a status that has moved on (`brain/bin/reconcile` catches these).

## Step 5 — amend, don't delete

`brain/knowledge-catalog/howto.md`: *"Supersede, don't delete."* For a decision
still right in substance but wrong in detail, set `status: amended` and add a
dated amendment section — the original reasoning stays readable and the
correction says what changed. For one that is now wrong, `status: superseded`
with `superseded_by:` pointing at the replacement, so the next walk carries a
reader there automatically.

A decision quietly edited to match what got built teaches a reader that the
record is written afterwards.

## What to produce

1. **Termination**, if the walk did not reach Intent. First, always.
2. **Drift**: the promise, the sentence, and whether the widening was intended.
   One line each.
3. **Omission**: what the forward walk shows outstanding.
4. **The amendment list**: every upstream document the build has made false,
   with what is now wrong in it. This is the deliverable, not a footnote.

Then offer to make the amendments. They are small, and now is when they are
worth making — two days later nobody remembers which claim moved.

## Closing the task

This skill owns `Testing → Complete`
([D0007](../../knowledge-catalog/decisions/D0007-status-ownership.md)). When
the review passes, set the status and append a `# Log` entry saying the review
ran and what it found.

**Offer a retro; never require one.** An Evidence node (`type: evidence`, with
`reviewed:` naming what it covered and `amends:` naming what it corrected) is
the richer form, and a team usually wants it. A solo operator holds the context
already and a retro is pure overhead — so the gate is that a review *happened*,
not that a node exists. One retro per **initiative**, appended to as tasks are
reviewed, is almost always better than one per task: the amendment list is
interesting at initiative scale and mostly empty at task scale.

## When there is no drift

Say so plainly and stop. A review that manufactures a concern to look thorough
costs more than it is worth: the next one gets skimmed.
