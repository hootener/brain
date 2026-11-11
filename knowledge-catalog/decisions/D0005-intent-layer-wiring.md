---
id: D0005
type: decision
title: Approach nodes name the goal they serve, and decisions carry a machine-readable claim
description: "serves on plan and spec is the typed edge that makes the Intent layer reachable; invariant and constrains let a decision be found by the change it governs rather than by term matching."
timestamp: "2026-11-07"
tags: [brain, intent, graph, lint]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "Every Approach node names at least one goal, so every lineage branch terminates in Intent."
constrains: ["bin/ancestry", "bin/constraints", "bin/lint", "skills/to-spec", "skills/grill-change"]
---

# Context

**The Intent layer was unreachable from the Action layer.** Measured on a
five-task fixture in this repository: `bin/ancestry` reaches a decision only
through a prose body link, and no skill writes one. Deleting two body links
from a spec dropped the closure to six documents with **zero decisions and
zero goals — and no warning**. `/spec-review` and `/code-review`, the two
skills whose stated purpose is "does this still serve the why", were reading a
chain that stopped one link short of the why.

Separately, `bin/relevant` prints **every decision on every call** because its
own docstring admits term matching misses the one that matters. At seventeen
decisions that is four lines; at eighty it is noise, and noise is not read.

# Decision

**`serves:` on `plan` and `spec` only — never on `task`.** A task's intent
arrives through its spec. This puts the field where someone is already
thinking about *why* (writing a spec) rather than where they are thinking
about *what* (writing a ticket), and it means `/to-tasks` needs no new field:
the chain is task → spec → goal.

The walk could reach a goal by following prose, and does today. Three reasons
to make it a typed edge anyway: a field is enumerable and prose is not; a
typed edge is precise where a body link merely means "mentioned"; and where a
task is served by several goals and decisions, `serves:` is how the author
says which is **primary** — a distinction `bin/ancestry`'s `via` field is
already reaching for and cannot express.

**Decisions carry a machine-*surfaceable* claim**: `invariant:`, one sentence a
change could violate, and `constrains:`, the paths or concepts it governs.
`bin/constraints <changed paths>` then lists the decisions in force for *this*
change, and the print-them-all fallback retires on its own.

**A contradiction needs a recorded exit.** `/grill-change` already sorts hits
into Contradictions, Reinforcements and Unexamined, but a contradiction could
be noted and then forgotten. There are now two exits and no third: amend the
decision, or proceed against it with the id recorded in the task's `# Log`.
`bin/lint` errors on a task that touches a constrained path and cites no
decision. That is the teeth; everything before it is etiquette.

**Lineage completeness is checkable.** Every branch of a reverse walk must
terminate at a `goal` or a `principle`. A branch that dies at a spec is
**unterminated**, and that is a finding about the record reported above any
code finding, because it means work shipped with no recorded intent.

# Alternatives

- **Rely on body links** — rejected: measured to fail silently, which is the
  worst failure shape.
- **`serves:` on tasks too** — rejected: duplicates what the spec already
  says, at the layer with the most files and the least thought about why.
- **Synonym lists or embeddings in `bin/relevant`** — rejected: moves the
  term-matching gap rather than closing it. A decision that declares what it
  governs does not need to be guessed at.
- **Contradiction as advice only** — rejected: the record already contained
  decisions that had been contradicted and never amended.

# Consequences

- `bin/ancestry` follows `serves:` as a lineage edge and reports unterminated
  branches as a count.
- `bin/lint` warns on an Approach node with no `serves:`.
- `/to-spec` must resolve or create a goal before writing a spec, which is the
  first point at which the absence of one becomes visible.
- `/grill-change` gains a required exit for every contradiction it raises.
- `bin/relevant` keeps the full decision index only until `bin/constraints`
  covers the same ground.

## 2026-11-10 — amended: surfaceable, not checkable

"Machine-readable" was an overclaim and it cost a fourth adopter a false
decision on their first day. They wrote an `invariant:` asserting every resume
PDF is at most two pages. One was three pages. **`bin/lint` passed and
`bin/stale` reported "Nothing contradicts the repository"** — because nothing
validates an `invariant:` against the artifact it governs, and nothing can in
general: it is a sentence about the world, not a fact about the tree.

They caught it by *reading* `bin/constraints` output, which is the only
instrument there is.

So the word is **surfaceable**. `invariant:` means a tool can put the claim in
front of you when you touch the paths it governs; it does not mean anybody has
checked it. Calling it machine-readable invites exactly the belief that it has
been — the stale-but-confident note D0001 exists to prevent, written into the
field that was supposed to prevent it.

The one case that could be mechanised is a *countable* invariant measured
against a per-project fact, which is what
[T0004](../../tasks/formalize/T0004-give-bin-stale-a-per-project-facts-file.md)
proposes. That task was speculative when written and now has a cost attached.
