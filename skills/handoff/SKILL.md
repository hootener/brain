---
name: handoff
description: Produce a self-contained HTML handoff document for one initiative and one discipline — a page you can send to a designer, an engineer, an executive or an analyst who has never opened the brain and has no intention of opening it. Use when work is being passed to someone else, at any phase.
---

Somebody is about to pick up work you have been holding. This makes the page
you send them.

**The reader does not know what this brain is and should not have to care.**
The output is one HTML file plus its images: no build, no server, no network,
opens from a thumb drive. Nothing in it links anywhere they cannot reach.

## Two things this is not

**It is not required.** Nothing in this brain checks that a handoff exists. A
solo operator taking something end to end does not need one; plenty of work is
too small to warrant one. `bin/handoff` checks the record is in a state to
produce an *honest* handoff when you ask for one — it never asks you to want
one.

**It is not a node.** `graph.html` is not a node and neither is this. The
*document* is a projection of the graph, regenerated whenever the graph moves.
What is worth recording is the **event**: that on a date, this was handed to
these people, promising this. That is evidence, and step 5 writes it.

## Step 1 — check before asking anything

```bash
brain/bin/handoff <initiative> --check
```

Report what it says before you ask the operator a single question. They may
want to fix something first, or may knowingly proceed.

It **blocks** on only two things, both of which mean the page would be
dishonest: no plan behind the initiative, or a plan with no `serves:`, so there
is no stated intent for the handoff to be checked against.

Everything else is a note, not a failure. In particular, **outstanding tasks
are normal** — a design handoff happens *before* the work, so of course nothing
is done. Say so; do not treat it as a problem.

## Step 2 — ask who it is for, in their words

Not "pick a discipline". Ask who is receiving this and what they are going to
do with it:

> *Who is picking this up, and what do they need to do with it?*

Then distil the answer yourself into one of: `engineering`, `design`,
`product`, `business`, `executive`, `marketing`, `analysis`. Say which you
chose and why, in one line, so they can correct you.

The discipline shapes the sections. The actual recipient — *"Priya, contract
squad"* — is a person, and goes in the evidence node at step 5, not in the
enum.

## Step 3 — propose what goes in, with answers already filled

Present it as a numbered list with your recommended answer on each, the way
`/grill-change` does. The operator overrides what they disagree with; silence
means your answer stands.

```
❓ **1 — Constraints.** I will carry D0002, D0005 and D0007 as the decisions
   that bind this, taken from what the plan and its tasks cite.

➡️ Include all three. D0007 is the one a newcomer most often violates.

---

❓ **2 — Outstanding work.** Seven of eight tasks are open...
```

Cover at least: which decisions appear as constraints, what is named as
outstanding, what is explicitly out of scope, what is deliberately still open,
and whether anything in the record should be held back from this audience.

**Propose, do not interrogate.** Five questions with answers attached is a
minute of their time. Five open questions is a meeting.

## Step 4 — name what only a person can write

The generated half comes from the graph. The authored half lives in
`brain/plans/<initiative>/handoff/<discipline>.md` and the generator never
touches it. Its headings match the section titles.

Tell the operator plainly which sections are empty and why, and offer to draft
them from what you know:

- **The first hour** — where things live, what to read, in what order
- **Walkthrough** — the screenshots, in sequence, each caption a plain sentence
- **Traps that cost us time** — *the single most valuable section in any
  handoff, and the one no graph will ever produce.* Ask for it directly. "What
  went wrong that you would warn them about?"
- **Verified, and not** — what was actually checked, and what was not
- **Running it yourself** — how to get it going locally

An empty section is omitted rather than rendered blank, so a thin handoff
looks thin rather than broken.

## Step 5 — generate, then record the event

```bash
brain/bin/handoff <initiative> --for <discipline>
```

Then write the evidence node — `brain/bin/new evidence "Handoff to <audience>"`
— carrying `audience:`, `handed_to:`, `rendered:` and `append_only: true`.

**One node per (initiative, audience).** A second send to the same audience
appends a dated line to that node's log. A different audience gets its own
node, because the promise genuinely differs.

The rule for which:

> **Append when the document got better. Supersede when the promise changed.**

A new screenshot or a clearer sentence is an append. A cut scope or a moved
date is a different promise: `status: superseded`, `superseded_by:` the new
node, so a reader who lands on the old one is carried forward.

## When the record is not ready

Say so plainly and stop. The two blocking cases both mean the same thing —
there is no recorded intent this page could be checked against — and a handoff
written anyway is a confident document with nothing behind it, which is the one
failure this brain exists to prevent.

Writing the missing `serves:` takes a minute. Offer that instead.
