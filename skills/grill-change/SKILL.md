---
name: grill-change
description: Interrogate a proposed code change against the project's own decision record, experiment results and open tickets before any code is written. Use when someone describes a change they are about to make, asks whether a change is a good idea, or says "grill me" about an approach.
---

Somebody is about to change this codebase. Before they do, find out what the
project has **already decided, already measured, and already got wrong** about
the thing they are proposing — and put it to them.

The point is not to be cautious. It is that this project keeps a record of its
own reasoning under `brain/`: decisions with their rejected alternatives, the
brief it answers, tickets including the ones deliberately marked Not Ready, and
a running account of what has gone wrong. A change proposed without reading
that is a change that will re-litigate a settled question, or re-break
something already broken once.

## The rule that makes this worth running

**Every challenge cites a path.** Not "this might conflict with your
architecture" — `brain/knowledge-catalog/decisions/NNNN-<slug>.md` says X, and
here is the line.

A model asked to "consider the existing decisions" will produce something
decision-shaped whether or not it read any. A citation can be checked. If you
cannot name the file, you are offering an opinion, and you must label it as one.

## Step 1 — ask the record, out loud

Run it. Show the output. Do not paraphrase from memory:

```bash
brain/bin/relevant "<the change, in their words>"
brain/bin/constraints <the paths this change will touch>
```

`relevant` searches by term and will miss a decision that uses different words
for the same thing. `constraints` does not search: it lists the decisions that
have **declared** they govern these paths, with the one sentence each says must
stay true. Run both — neither covers the other.

`relevant` ends by naming the decisions that matched **neither** your words nor
any path. Those are the ones no tool here can surface against a change, so read
that list yourself; it is short when the record declares what it governs and
long when it does not, which is itself the finding.

Then run it again on the *domain* words if the first pass was thin — the record
uses the project's vocabulary, so "make it faster" finds nothing while "cache",
"repeats", "call budget" find plenty.

**Read the files it returns.** Not the one-line excerpts — the actual
documents, including the rejected alternatives, which is where the reasoning
usually is. A decision's "Alternatives considered" section frequently contains
the exact proposal being made today.

Also check, every time, regardless of what `relevant` returned:

- `brain/knowledge-catalog/reference/E0001-brain-in-practice.md` — what has already
  gone wrong here, written down as it happened
- `brain/brief/` — the assignment itself; a proposal that quietly drops a stated
  constraint is the commonest contradiction in a brief-driven project
- `brain/tasks/<initiative>/` for anything **Not Ready**, which usually means
  deliberately deferred with a reason rather than not thought about yet

## Step 2 — sort what you found into three piles

Say which pile each hit lands in. The piles are not the same shape and must not
be presented as one list.

**Contradictions.** The record has decided against this, or measured it and
found it did not work. Name the decision **by id**, quote the line, and say what
would have to change for the proposal to stand.

**A contradiction has exactly two exits, and "noted" is not one of them**
([D0005](../../knowledge-catalog/decisions/D0005-intent-layer-wiring.md)):

1. **Amend the decision** — set `status: amended` and add a dated amendment
   saying what changed and why, or `superseded` with `superseded_by:` when it
   is now wrong. A legitimate move, often the right one, and cheap.
2. **Proceed against it** — with the decision's id recorded in the task's
   `# Log`, so the next reader finds a deliberate exception rather than an
   accident.

Do not close this skill with a contradiction still floating. Before this rule,
a contradiction could be raised, agreed with, and then forgotten by everyone in
the conversation including you. *"That decision named
this as its defined exit; you are proposing to take the exit, so this is an
amendment and not a contradiction"* is a completely different conversation from
*"that decision rules this out, and here is the line, and here is the time it
broke."*

**Reinforcements.** The record already argues for this, or an experiment
already measured a benefit. Say so, with the number. A proposal that the
evidence supports should be made easier, not interrogated for symmetry.

**Unexamined.** Genuinely new ground. Say that plainly; it is the most useful
thing you can tell someone, and the temptation is to manufacture a concern so
the grilling feels thorough.

## Step 3 — grill, in rounds

Work the open questions as a tree. The **frontier** is every question whose
prerequisites are settled — the ones answerable now without guessing at answers
you have not heard. Ask the whole frontier in one round, numbered, each with
your recommended answer. Then wait.

```
❓ **Q1** — **<question title>**: <the question, and what in the record
prompts it>

➡️ <your recommended answer, and why>

---

❓ **Q2** — …
```

Each round's answers reshape the tree. Recompute the frontier and ask the next.
A question whose answer depends on another still open in this round belongs to a
later round.

**Find facts yourself.** If a question needs something from the codebase — does
this service get container-resolved, how many tests touch this file, is there
already a config flag — go and look. The *decisions* are the human's; the facts
are yours. Never ask them something `grep` can answer.

## Step 4 — the specific things worth asking about in this project

Not a checklist to recite. `brain/knowledge-catalog/reference/E0001-brain-in-practice.md`
is where the places changes have actually gone wrong here accumulate; read it
and ask about those first. Until it has entries, these have earned a look in
every project that has used this skill:

- **Does this drop a constraint the brief states?** The brief is the one
  document nobody on the project wrote, so it is the one most easily argued
  past. Quote the sentence.
- **Does this put a judgement where a lookup would do?** A model asked to
  supply a value from an open set when a closed one exists. Each time it has
  been found, the fix was to enumerate the set.
- **Does it make an existing claim false?** Decisions, task statuses, counts in
  README files, and comments in code. `brain/bin/stale` catches the countable
  ones; nothing catches a mechanism except reading.
- **What is the blast radius, in tests?** Name the files that will go red,
  before running anything.

## Step 5 — land it

Stop when the frontier is empty, and produce:

1. **A verdict in one line.** Proceed, proceed-with-an-amendment, or don't — and
   if an amendment, which decision and what it should now say.
2. **The citations**, as a short list of paths, so the reasoning is checkable
   after the conversation.
3. **A task file** under `brain/tasks/<initiative>/`, if the change is going
   ahead — following the template of its neighbours, with `blocked_by` wired to
   anything it genuinely waits on.

Do not write code. This skill ends where the change begins; the pull to start
implementing is the signal it has done its job.

## When the record is wrong

It happens, and it is the most valuable outcome. If the proposal is right and a
decision is stale, say so directly and amend the decision with a dated note.
An amendment is cheap. A record nobody trusts because
it is quietly out of date is not.
