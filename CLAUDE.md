# Working with this brain

The project's reasoning lives here as linked markdown with YAML frontmatter —
a graph of typed nodes, no database and no service. The files are the ground
truth, which is what makes them equally legible to a person, a diff and you.

**One principle underneath everything**
([the principle that claims must be checkable](knowledge-catalog/decisions/D0001-claims-must-be-checkable.md)): the
record is only worth keeping if its claims are checkable. An absent note costs
a reader nothing — they go and look. A confident, stale note costs them the
thing they would have found by looking.

## The shape

Four categories, left to right. Implementation runs forward; review runs back
and amends what it finds false.

| Category | Types | Goes in |
|---|---|---|
| **Intent** | `goal` `decision` `principle` | `knowledge-catalog/goals/`, `knowledge-catalog/decisions/` |
| **Approach** *(optional)* | `plan` `spec` | `plans/`, `knowledge-catalog/specs/` |
| **Action** | `task` | `tasks/<initiative>/` |
| **Evidence** | `evidence` | `knowledge-catalog/reference/`, `brief/` |

Plus `glossary` — definitional, outside the four.

**Approach is optional, and a brain with no plans is a correct brain.** Counted
across five brains, four have none: where intent arrives from outside as prose
— consulting, client work, solo research — a `task` serves a `decision`
directly and the lineage still terminates. `decision`, `task` and `evidence`
are 93% of all nodes written so far and are the three to reach for first.

**Nothing cites a node newer than itself.** A goal written today may cite
evidence from last quarter; editing that older node to point back at the new
goal is rewriting history. `superseded_by` and `amends` are the sanctioned
exceptions, because a reader who reaches a stale node must be carried to its
replacement.

**Ids** are a type letter plus four digits, flat and global: `G` goal, `D`
decision *and* principle, `P` plan, `S` spec, `T` task, `E` evidence. Always
allocate with `bin/new`; never pick a number by hand.

## The fields a tool reads

Everything below is read by something. **Nothing else is.** A field you invent
is inert — three adopters wrote `supersedes`, `intent:` and `owner`, all
reasonable, none read by anything, and nothing said so until `bin/unread`
existed. If a field here does not apply, **delete the line**; an unfilled
`<placeholder>` is a lint error.

| field | on | read by | absent means |
|---|---|---|---|
| `serves:` | plan, spec, task | `ancestry`, `lint`, `handoff` | the lineage never reaches Intent; `ancestry` reports `⚠ UNTERMINATED` |
| `invariant:` | decision, principle | `constraints`, `relevant`, `handoff` | no one-sentence claim a change could violate |
| `constrains:` | decision, principle | `constraints`, `index` | **the decision can never match a diff** — it exists but no tool will ever surface it |
| `blocked_by:` | task | `status`, `ancestry` | nothing is waiting on anything (D0008 — a blocker is a node, not a sentence) |
| `category:` | task | `lint`, `triage` | not `bug`, `enhancement` or `question`; an open question needs `question` (D0009) |
| `claimed_by:` | task | `status` | nobody holds it; `status` shows it as claimable |
| `superseded_by:` | any | `lint`, `ancestry`, `relevant` | a reader reaching the stale node is not carried to its replacement |
| `amends:` | evidence | `lint`, `graph-build` | a retro that corrects something upstream does not say what |
| `append_only:` | evidence | `lint` | a running record trips the direction rule on every entry |
| `reviewed:` | evidence | `lint`, `graph-build` | a looking-back pass does not name what it covered |
| `resource:` | evidence | `lint`, `graph-build` | evidence that was *given* does not point at the original |
| `initiative_plan:` | plan | `handoff` | with several plans in an initiative, `handoff` has to guess which one |
| `notes:` | evidence | `note` | `bin/note` cannot find the running record and opens another |
| `human_fields:` | `config.md` | `unread` | a field people read deliberately is reported as dead on every run |

**The brain describes one repository.** A node may point at an artifact
outside it — `resource:` does this, and `bin/lint` errors when the target does
not exist — but **nothing here verifies that file still says what the node
claims**. Write such a claim dated and in the past tense: *"the profile at
`~/.claude/...` said X on 2026-11-10"*, not *"the profile says X"*. A finding
once sat correctly captured in a task log for eight hours while the file it was
supposed to change stayed wrong, because a complete record reasonably read as a
landed change (D0014).

`constrains:` is the one most often missing and the most expensive: one adopter
had it on 1 of 23 decisions, so the check `CLAUDE.md` asks for before every
change returned nothing every time they ran it.

## Which kind of node is this

Two questions settle most of it. `bin/which` asks them, and takes answers
directly — `bin/which n y` — so this is one call, not a prompt.

1. **Does it bind future work** — a choice someone made, yours or a client's,
   that a later change could violate? → **decision**. The test is the
   violation, not who chose: a client setting a policy is a decision even
   though you were *told* it.
2. **Has it already happened**, and binds nothing going forward? → **evidence**.

Then: work somebody could finish → `task`; an outcome you could later check you
reached → `goal`; a rule across cases → `principle`; the order work gets done
in → `plan`.

**None of them clearly? Do not force one.** `bin/note "<what happened>"` takes
it with no type at all, and an entry that stays a journal line still did its
job (D0012). A wrong type is worse than no type, because a typed node looks
decided.

## Reflexes — these fire without being asked

- **Before any significant change**: `bin/relevant "<the change>"` **and**
  `bin/constraints <paths>`. The first searches by term and misses what the
  record words differently; the second lists decisions that declared they
  govern those paths.
- **Before writing code for a task**: `bin/ancestry <id> --claims`. If it says
  `⚠ UNTERMINATED`, the task has no recorded intent — say so before building.
  If an ancestor says `⚠ SUPERSEDED`, build against the replacement.
- **A contradicted decision is stated, never silently overridden.** Two exits
  only: amend it, or proceed against it with the id in the task's `# Log`.
- **Supersede, don't delete.** A decision quietly edited to match what got
  built teaches the reader that the record is written afterwards.
- **When you learn something the record doesn't know**, write it down then.
  `bin/note "<what happened>"` takes it with no type, no folder and no id —
  use it by default. Deciding what kind of thing it is costs more than writing
  it, so do that later, with `bin/which`, or never: an entry that stays a
  journal line still did its job. Reach for `bin/new <type>` when you already
  know the answer.

## Tools

| | |
|---|---|
| `bin/lint` | the gate: taxonomy, ids, links, direction, frontmatter hazards |
| `bin/doctor` | is the install sound |
| `bin/status [initiative]` | task table and the claimable frontier |
| `bin/relevant "<change>"` | what the record already says about a proposal |
| `bin/constraints <paths>` | which decisions govern these files |
| `bin/ancestry <id> [--claims]` | walk back to the intent that asked for it |
| `bin/ancestry <id> --forward` | walk on to what that intent asked for |
| `bin/reconcile [initiative]` | task status against the repository |
| `bin/note "<what happened>"` | record it now; decide what it is later |
| `bin/which` | what kind of node is this — the procedure, in two questions |
| `bin/new <type> "<title>"` | allocate an id and scaffold the node |
| `bin/stale` | countable claims that no longer match the files |
| `brain/facts.md` | *(optional)* counts your project can produce, so `stale` can check them |
| `bin/prove <cmd>` | does that tool's output match what it did to the files |
| `bin/unread` | frontmatter the record carries that no tool reads |
| `bin/index` | rebuild folder indexes (`bin/new` calls it) |
| `bin/graph-build` | rebuild `graph.html` |
| `bin/handoff <init> --for <who>` | a self-contained page for someone outside the brain |

When an initiative holds more than one plan, mark the initiative's own with
`initiative_plan: true` — `bin/handoff` otherwise has to guess which one a
handoff is about, and it says so when it does. `--plan <path>` overrides.

## Skills, by where they sit on the picture

**Forward** — `/grill-change` → *(`/to-spec`)* → `/to-tasks` → `/implement`

`/to-spec` is for work with a planning phase. Where intent arrives from outside
as prose — consulting, client work, a decision taken in conversation — go
straight from `/grill-change` to `/to-tasks`, and let each task `serves:` the
decision it binds to (D0013). Four of five brains measured have never written a
spec; that is the normal path, not a shortcut.
**Back** — `/spec-review` (owns `Testing → Complete`), `/audit`
**Off the rails** — `/interrogate` (ask the brain anything), `/triage`,
`/wayfinder`, `/research`, `/domain-modeling`, `/code-review` (the artifact,
not the graph)

## Don't

- Hand-edit an index or a log where a skill would write it.
- Renumber anything. Ids are cited from journals, logs and commit messages.
  `bin/renumber` exists only to resolve a collision.
- Write into the host repository's own `CLAUDE.md`, `CONTEXT.md` or
  `docs/adr/`. Those are the team's: read and respect them.
- Put code in the brain, or a restatement of the code. This holds the *why*
  that points at the code.
- Fill a gap with something decision-shaped. "We didn't decide that" is an
  answer.

Depth lives in [knowledge-catalog/howto.md](knowledge-catalog/howto.md) and
[knowledge-catalog/reference/agent-skills/](knowledge-catalog/reference/agent-skills/index.md).
