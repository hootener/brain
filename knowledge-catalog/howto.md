# How to use the knowledge catalog

The durable record of goals, decisions, plans and findings for this project.
Every entry is markdown plus YAML frontmatter in Google's Open Knowledge Format
(OKF), organized as a cross-linked graph.

## How it's used

An agent **searches this catalog before making a significant decision**
(`brain/bin/relevant`, `brain/bin/constraints`) and surfaces the relevant
entries, with followable links, as justification. During normal work it
**passively captures** durable decisions here and reports what it wrote at the
end of the session.

## If you're hand-editing these files, you're probably doing it wrong

The intended path is through the skills — `/grill-change` before a change,
`/to-tasks` to cut the work, `/spec-review` after, `/audit` to reconcile.
`/to-spec` sits between the first two when the work has a planning phase, and
four of five brains built so far have never used it.
They handle the format, the taxonomy, the ids and the cross-linking;
`bin/new` keeps the indexes, the change log and the graph in step, and
`bin/index` rebuilds any folder index from the nodes present.

You *can* hand-edit; it is only markdown. If you do, run `brain/bin/lint`. It
blocks malformed entries, and everything below is something it checks.

## The four categories

Every node belongs to exactly one, and they run left to right
([D0002](decisions/D0002-four-categories.md)):

| Category | Types | Answers |
|---|---|---|
| **Intent** | `goal`, `decision`, `principle` | what we are trying to do, and what we have settled |
| **Approach** *(optional)* | `plan`, `spec` | how we mean to do it |
| **Action** | `task` | what gets done |
| **Evidence** | `evidence` | what we observed, and what it corrects |

**Approach is optional.** A brain with no plans is a correct brain: where the
intent arrives from outside as prose, a task names the decision it serves and
the lineage still terminates. Four of five brains have no plans at all, and
`decision`, `task` and `evidence` are 219 of 235 nodes written across them
([the decision that Approach is optional](decisions/D0013-approach-is-optional-and-three-types-carry-the.md)).

`glossary` sits outside the four: it is definitional, not a claim about the
work.

**Nothing cites a node newer than itself.** Intent → Approach → Action is the
usual order because intent usually comes first, but a goal written today may
cite evidence an earlier effort produced. What is forbidden is editing that
earlier node to point at the new one, because a reader would then see a
justification that did not exist when it was written. Two edges are sanctioned
exceptions — `superseded_by` and `amends` — because a reader who reaches a
stale node must be carried to what replaced it.

## Ids

A type letter plus four digits, flat and global
([D0003](decisions/D0003-identifiers.md)):

`G` goal · `D` decision *and* principle · `P` plan · `S` spec · `T` task · `E` evidence

The id lives in the frontmatter **and** as the filename prefix, and the linter
checks they agree. `brain/bin/new <type>` allocates it; never pick one by hand,
and never renumber except with `brain/bin/renumber` to resolve a collision.

## Types and their body headings

| Type | Headings |
|---|---|
| `goal` | `# Goal` · `# Why` · `# Signals` |
| `decision` | `# Context` · `# Decision` · `# Alternatives` · `# Consequences` |
| `principle` | `# Principle` · `# Rationale` · `# Implications` |
| `plan` | `# Goal` · `# Approach` · `# Sequence` · `# Risks` |
| `spec` | see [templates/spec.md](reference/agent-skills/templates/spec.md) |
| `task` | `# What to build` · `# Acceptance criteria` · `# Out of scope` · `# Log` |
| `evidence` | free-form prose, optionally `# Citations` |

`index.md`, `log.md`, `howto.md`, `README.md`, `TODO.md`, `map.md` and the
three agent-skills configuration files are reserved — no `type`, no `id`.

Templates for each live in
[reference/agent-skills/templates/](reference/agent-skills/templates/).

## Status

Two vocabularies, disjoint by type. A `task` uses the tracker's lifecycle
(`Not Ready` → `Ready for Development` → `In Progress` → `Testing` →
`Complete` → `Delivered`, plus `Superseded`). Everything else uses the document
lifecycle: `draft`, `active`, `amended`, `superseded`, `deprecated`. `amended`
applies only to a decision or a principle, and exists so a decision corrected
in detail does not read as untouched.

## Conventions

- **Supersede, don't delete.** When a decision changes, set
  `status: superseded`, link the replacement in the body, and record it in
  [log.md](log.md). Hard-delete only genuinely erroneous entries.
- **An Approach node names the goal it serves.** `serves:` on a plan or a spec
  is what lets a reverse walk terminate in Intent. Without it, `bin/ancestry`
  stops at the spec and the review has no why to check against.
- **A decision declares what it governs.** `invariant:` is one sentence a
  change could violate; `constrains:` lists the paths it covers. That is how
  `bin/constraints` finds it without guessing at vocabulary.
- **A running record carries `append_only: true`.** A project log, an
  open-questions list, a state snapshot: its content legitimately runs ahead
  of its timestamp, so the direction rule leaves it alone. A `task` gets this
  for free — it has a `# Log` by construction.
- **An import dates a node by its origin, never by the import.** Stamping
  today's date on a document written in July makes every decision that cites
  it look like a violation. Take the date from the filename, or from git's
  first-commit date — which is why an import uses `git mv`.
- **A running record is dated when it was opened, not when it was last
  appended.** Each entry carries its own date; the frontmatter carries the
  file's. Re-dating on every append would invalidate every citation of it,
  because the direction rule would correctly see an older node citing a newer
  one.
- **Cite a node with a link, never in backticks.** A backticked citation is a
  lost edge: invisible to the graph, unfollowable by a reader, and unchecked
  by anything, so it rots the first time something is renamed. `bin/relink`
  converts them. A bare file *name* in prose — `config.md` — is not a citation
  and should stay as it is.
- **Anything measured records the measurement**, with its denominator and its
  date, so a future reader can re-check it rather than believe it.
- **A stale note that reads as current is worse than no note.** Correct in
  place and say what changed. `brain/bin/stale` catches the countable cases;
  `brain/bin/reconcile` catches status against the repository.
- **Source material is quoted, analysis is linked.** The brief lives verbatim
  under `brain/brief/` as `evidence` with a `resource:`; anything derived from
  it says so and shows the arithmetic.
- **Quote any frontmatter value containing `#` or `: `.** Unquoted, a ` #`
  opens a YAML comment and silently truncates the value, and a `: ` is a hard
  parse error. The linter catches both.
- **Use relative links** (`./sibling.md`, `../decisions/x.md`). A link starting
  with `/` resolves from the repo root on GitHub and 404s; the linter errors on
  those.
- **A blocker is a node, not a sentence** (a blocker is a node). Another task, a whole plan
  (which clears when its initiative is finished), or — for anything external —
  the actionable task hiding inside it, which is usually an email. A reason
  that is neither is a constraint or a note, not a blocker.
- **An open question that blocks work is a task** with `category: question`
  (an open question is a task). One that blocks nothing stays evidence. There is no type for a
  question.
- **A fix lands with its check.** Anything a real adoption reports and that
  changes code gets a named case in `bin/selftest`'s regression layer. Four
  rounds of feedback produced roughly twenty changes and five checks, and the
  same three classes — a parser, a laundering path, an install recipe — each
  came back twice. A finding without a check is a finding you will be told
  about again.
- **A handoff is a projection, not a node.** `bin/handoff` renders one from
  the graph plus a hand-written half under `plans/<initiative>/handoff/`. What
  gets recorded is the *event* — handed to whom, when, promising what — as an
  `append_only` evidence node, one per audience. Append when the document got
  better; supersede when the promise changed. **Nothing ever requires a
  handoff to exist.**
- **Link liberally.** A link to a document not yet written is a valid marker of
  future work.
