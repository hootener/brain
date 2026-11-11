---
id: D0012
type: decision
title: "Capture is separate from classification, and capture wins by default"
description: "Three adoptions put the brain's overhead at 15-25% per change, and the third located it: writing is cheap, choosing the category is the cost."
timestamp: "2026-11-10"
tags: [ceremony, capture, adoption]
status: active
invariant: "Recording something the record does not know requires no decision about what kind of thing it is."
constrains:
  - bin/note
  - bin/which
  - bin/new
  - knowledge-catalog/reference/agent-skills/**
---

# Context

Three adopters reported the same overhead and the third measured it:
**15–25% per change**, and they located it exactly.

> Writing is cheap, choosing the category is the cost and has no tool.

That is the whole finding. Every other decision in this brain has a tool —
`bin/new` allocates the id, `bin/lint` checks the shape, `bin/constraints`
finds what governs a path — and the decision taken most often, several times a
day, had none. Seven types, most of them wrong for any given thing, chosen
while the thing is still happening.

The same project reported what actually earned its keep, and it was not the
typed graph:

> The project log carried the day — an append-only prose journal, barely a
> typed node.

It caught a wrong cancellation policy before it reached every new family,
stopped a link that would have silently broken acknowledgement tracking, and
turned a second mislabelled notification into "this is a pattern". Meanwhile
`constraints`, `ancestry`, `stale`, `reconcile` and the task graph went unused,
because the real unit of work was *"what did the client just say in email"*.

So the costly step is the one with no payoff at the moment it is paid, and the
valuable step is the one the model treats as an afterthought.

# Decision

**Capture takes no type.** `bin/note "<what happened>"` appends a dated,
untyped entry to a running record, creating that record on first use. No
folder, no id, no required headings, no `serves:`. One command, and the gate
stays clean — measured: a fresh `bin/note` entry lints at zero errors, where a
fresh `bin/new evidence` node lints at two until a person hand-edits the
scaffold away.

**Classification is a later, optional step with its own tool.** `bin/which`
asks the discriminating questions in the order that resolves fastest — has it
already happened; did you choose between alternatives that a change could
violate — and names the type and the exact `bin/new` command. Two questions
settle most cases. `bin/note --review` lists what has not been promoted.

**Leaving an entry unclassified is a correct outcome, not debt.** A journal
line that never becomes a node still did its job the day it was written. The
review list is a menu, not a queue.

**Neither tool reads your text to guess.** D0011 holds: a tool inferring what
the adopter's content means is the failure class behind every semantic defect
found so far, and here the stakes invert — **a wrong type is worse than no
type, because a typed node looks decided.** You answer the questions; the tool
only knows the order to ask them in.

The running record is an ordinary `evidence` node carrying `append_only: true`
and a declared `notes: true` marker. **No new node type** — the brain gains a
cheaper door, not a different shape.

# Alternatives

**Classify at capture, with a keyword or model-based guess.** Rejected under
D0011, and more sharply: the cost being removed is the cost of being wrong
about the type, so a guess relocates the cost rather than removing it, into a
node that now looks settled.

**Fewer types.** Considered and rejected — the categories earn their keep in
the tools that traverse them, and adopters disputed where things sit, never
that the distinctions are real. The problem was never the taxonomy; it was
being made to use it at the worst moment.

**Leave it to habit, as before.** Rejected on evidence. Three adopters, three
reports of the same overhead, and the one who measured it watched the typed
graph go unused while the untyped journal did the work.

**A separate inbox file outside the catalog.** Rejected: it would be a second
place to look, invisible to `bin/relevant` and `bin/lint`, and the thing most
worth finding later would be the thing least indexed.

# Consequences

The default path into the brain is now one command with no decision in it, and
the brain's own instructions say so — `CLAUDE.md`'s reflex is `bin/note` first,
`bin/new <type>` only when the answer is already known.

**The honest cost is that the record will hold more unclassified prose**, and a
brain that never promotes anything degrades toward being a diary with a linter.
That is the correct trade against what it replaces — three projects' evidence
is that the alternative was not a well-classified record but a thinner one,
because capture did not happen at all.

Nothing measures whether promotion actually happens. `bin/note --review` shows
the backlog; no gate objects to it growing, deliberately, since objecting would
reintroduce the obligation this removes.

**This decision is confirmed by a corpus that cannot test it, and that is
recorded here so nobody mistakes the confirmation for evidence.** The project
whose measurement produced it watched an unclassified prose question become a
decision node within minutes, because the thinking was already on disk — real
vindication of the capture half. But its only promotion mechanism is *a client
answering a question*, several times a week. A consulting brain has an external
forcing function; it is **structurally immune to the failure mode this decision
names**, which is a backlog nobody ever returns to.

Their own warning, and it is the right one: generalising from that corpus would
be reading the one project that cannot exhibit the risk. The capture half is
established. **The claim that leaving entries unclassified is safe is not**, and
the instrument for it is a project with no external party asking questions —
solo work, or an internal codebase where nothing arrives from outside to force
a promotion. D0011's own logic applies: a conclusion drawn from one corpus
shape is a conclusion about that shape.

**That project then ran it, and the answer narrows this decision rather than
confirming it.** There was no backlog — and the tool did not invent one, which
is worth saying because the honest result was available and reported. So the
feared failure mode did not appear in the corpus chosen for its ability to show
it. That is not evidence of safety; it is one session's absence of harm.

What it found instead is better than either answer. The operator believed a
finding had been lost because nobody captured it. It had been captured — a task
log, written minutes after the run it described. **Capture was not the
failure.** The finding needed to reach an artifact outside the repository
entirely — a voice profile under `~/.claude/` — and **no tool here reaches
across that boundary**: not `bin/note`, not `bin/new`, not any gate. The record
was complete and the thing it was supposed to change never changed.

So the risk this decision worried about — prose piling up unclassified — is not
the risk that materialised. The real one is a **correctly captured, correctly
classified finding whose target lives where the brain cannot see it.** The
backlog was never the right thing to watch.

See [the running record of what the brain has caught](../reference/E0001-brain-in-practice.md).
