# Installing and filling a brain

**This is the whole install.** The operator may have reached it by pointing you
at a URL with nothing on disk yet, or by running `bin/adopt` already. Step 0
tells the two apart. Everything after it fills the brain with the project.

It is a document rather than a skill on purpose
([why this is a document and not a skill](knowledge-catalog/decisions/D0010-init-is-a-document.md)): Claude Code
discovers skills when a session *starts*, so a skill created during this
session cannot be called by the session that created it. Three adoptions
proved that three times. Read this and follow it.

Installing and filling stay two mechanical steps, and `bin/adopt` is still the
one that installs
([the placement and install decision](knowledge-catalog/decisions/D0004-placement-and-install.md)).
What changed is that the operator types one thing instead of two: you run
`adopt` for them. They fail differently and rerun on different schedules —
`adopt` once per brain, this once per repository it describes — so keep them
apart when you rerun.

## Step 0 — is the brain on disk yet?

```bash
ls brain/config.md 2>/dev/null || ls config.md 2>/dev/null
```

**If it exists, the brain is installed.** Skip to the branch test below.

**If it does not, install it now.** Do not guess the placement — it is the one
thing the operator must choose, and it is not recoverable by moving files
afterwards, because `config.md` records the mode and the tools read it.

Ask them, in these words or close to them:

> Where should the brain live?
>
> - **Embedded** — `brain/` inside this repository. The default. The record and
>   the code it describes move in the same commit, so "this decision was true
>   at this commit" is checkable rather than asserted, and a reviewer meets the
>   decision in the pull request.
> - **Sibling** — its own repository beside the project, which you then run
>   from the containing folder. Required when you do not control the
>   repository (client work, a shared company project, an outside
>   contribution), or when one brain serves several repositories. Nothing is
>   ever written into the project repositories.

**Embedded**, the default. Copy the files; do not clone in place. A clone puts
a `.git` inside the brain and the host repository then tracks a *gitlink* it
cannot resolve, so the whole brain shows up as one unreadable entry rather
than as its files — invisible to the repository it is supposed to be versioned
with, which is the entire point of embedding it.

```bash
cd <project> && git init             # if it is not a repo yet. adopt requires one
git clone https://github.com/hootener/brain.git /tmp/brain-template
cp -R /tmp/brain-template brain
rm -rf brain/.git                    # the important line
brain/bin/adopt                      # add --with-ci to install the workflow
```

**Sibling.** Here a clone is right, because the brain *is* the repository.

```bash
cd <workspace>                       # holds the brain and the project repos
git clone https://github.com/hootener/brain.git brain
brain/bin/adopt --sibling
```

Then:

- Offer `--with-ci` rather than assuming it. `.github/` is the team's
  directory and the brain does not write there uninvited.
- Read `bin/adopt`'s output and repeat its closing instruction to the
  operator. For sibling it says to start Claude Code in the workspace rather
  than the project folder, which nothing else can do for them.
- `bin/doctor` fails if the `rm -rf` was skipped, so run it before continuing.
- **The skills are not loaded in this session** (D0010). Say so in your report
  rather than offering `/to-spec` and watching it miss.

Only when `config.md` exists do you carry on.

**Two paths, and you must pick one before step 1.**

```bash
ls brain/knowledge-catalog/decisions/ brain/knowledge-catalog/goals/   # a brain already?
git -C <repo> log --oneline | head -1                                  # code history?
ls <repo>/docs <repo>/CONTEXT.md                                       # docs?
```

**Ask about the brain first.** Three rounds running, a test that looked only at
the repository gave a confident wrong answer: no commits and no docs said
*greenfield* to a catalog already holding four nodes. The branches are about
what is already in the brain; the repository is the second question, not the
first.

**Step 3 runs whichever branch you are on. Always.** Conversation history is
independent of code history, and the branch test below does not look at it. A
repository with no commits and no docs had **five transcripts holding two real
decisions**, and the branch told its adopter those steps were no-ops. Run
`bin/utterances` before you conclude there is nothing to import — it
costs one command and it is the one place that had anything.

**Greenfield** — no commits, no docs. Steps 2 and 4 are no-ops; step 3 is not.
Go to "Greenfield" at the end and come back for step 5. The first adopter ran all four against an empty project, got
`fatal: your current branch 'main' does not have any commits yet`, and found
the real material was in the conversation they were already having.

**In flight** — a codebase with history, decisions living in people's heads,
and docs in four places. That is the rest of this skill.

**Re-adoption** — **the catalog already holds nodes.** That is the whole
test, and it comes before the other two. Neither branch fits: there is nothing to import that is not
already here, and mining the transcripts surfaces *the previous run's own
work*, which a second adopter duly filed as fresh findings. **Which of the steps below apply:**

| Step | On a re-adoption |
|---|---|
| 1 · ask the four things | **Skip 1–3.** `config.md` already holds them. Ask question 4 only if no goal exists. |
| 2 · import existing docs | **Skip.** Anything importable is already imported; re-importing duplicates it. |
| 3 · mine the transcripts | **Run, bounded.** See below — most of what is there is the previous run's own work. |
| 4 · mine git history | Run if there are commits since the last node. |
| 5 · report | **Run.** Say what changed and what you left alone. |

Read what is already in the catalog **first**, ask the operator what has
changed since, and write only that. Bounding it by date does not work: everything a re-adoption produces is dated
today, so `--since` returns what no filter returns. **The bound is already in
the catalog** — every imported node names the session it came from in
`resource:`. Read those first, and treat any utterance from a session already
cited as covered.

## The rule that governs everything here

**If the source already carries dates, trust them.** A brain migrating from
another brain — or any source already in OKF — has an authored `timestamp:` on
most of its files: 123 of 158, in the first migration of one. That date is
better than anything you can reconstruct, and `git log --follow` would have
been *worse* than what was already in the files. One line of work, and the
instruction below is for the case where the source has no dates of its own.

**Otherwise, date every imported node by its origin, never by the import.** Stamping
today on a document written in July makes every decision citing it look like
it cites the future, and the direction rule will say so — twenty-eight times,
in the first real adoption. Take the date from the filename where it carries
one, and from `git log --follow --format=%ad --date=short` otherwise, which is
the reason to move files with `git mv` rather than copy them.

**Mark running records `append_only: true`** — a project log, an
open-questions list, a state snapshot. Their content runs ahead of their open
date by design.

**An initiative's `plan.md` is usually one of them.** It is opened on day one,
links every phase as the phases arrive, and is appended to for weeks. In the
first migration, **25 of 27 direction warnings came from three plan files**,
every one spurious, and nothing told the migrator that a plan could be a
running record. If a plan links nodes written after it, it is.

**Everything imported lands as `status: draft` with a `resource:` pointing at
where it came from.** You are reconstructing, and a reconstruction that reads
as a decided decision is worse than no brain at all
([the principle that claims must be checkable](knowledge-catalog/decisions/D0001-claims-must-be-checkable.md)).
`bin/lint` reports the draft count, so the operator can see exactly how much is
still guesswork.

Never promote a draft to `active` yourself. That is the operator confirming it,
and it is the whole value of the import being visible.

## Step 1 — ask the four things you cannot infer

1. ~~Which repository?~~ **Do not ask.** `bin/adopt` already wrote it to
   `brain/config.md` as `repos:`. Asking invites an answer that contradicts
   the file.
2. Where do docs already live — `docs/`, `docs/adr/`, a `CONTEXT.md`, a wiki,
   an exported Notion space? → **write it to `docs:` in `config.md`.**
3. What issue tracker is in use, and should `brain/tasks/` mirror it or replace
   it? → **this is a decision, not a preference.** It has alternatives (mirror
   / replace) and a consequence (two places to keep in step). Write it with
   `bin/new decision`, and record the choice in `config.md` as `tracker:`.
4. **What is this project trying to achieve?** One sentence. → a `goal` node.

The first adopter answered all four and had somewhere to put one of them.
Questions 2 and 3 went into the change log because nothing else fitted.

**Do not proceed without the fourth.** Without a goal there is nothing for a
lineage walk to terminate at, `/spec-review` has no intent to check against,
and every Approach node you write will fail `serves:`. If the answer is "a lot
of things", take the two that matter most this quarter and write those.

Write it: `bin/new goal "<the outcome>"`.

### If there is no operator to ask

There may be nobody there. A fourth adopter ran this unattended, and **"do not
proceed without the fourth" has no exit for that** — so the only options the
document offered were to stop, or to invent an answer, which is the one thing
this whole file forbids.

The exit: **write the goal from what the operator has already said, quote the
sentence you took it from, and leave it `status: draft`.** A draft goal is an
honest proposal — it terminates a lineage walk, so the Approach nodes you write
are not orphaned, and `bin/lint` reports the draft count on every run so it
cannot be forgotten. Promoting it to `active` is the operator's, and say so in
your report.

Same for questions 2 and 3: if nobody can answer, leave `docs:` and `tracker:`
unset rather than guessing, and name both in your report as unanswered. An
empty field is a question anybody can see; a guessed one is indistinguishable
from a decision.

### The first session cannot finish a task

Skills load at session start (D0010), so the session that installs the brain
has none of them. `/spec-review` owns `Testing → Complete`, which means **work
done in the first session parks at `Testing` and cannot be moved on**, however
complete it is. That is not a bug to work around and not a reason to set
`Complete` by hand — the status means a named owner signed it off, and nobody
has.

Leave it at `Testing`, say so in your report, and let the next session close
it. A fourth adopter hit exactly this and reported it as a defect; it is a
consequence of D0010, and the fix is knowing it in advance.

## Node types this brain does not have

A source brain may carry types that are not in the seven. Two that have come
up, and what they become:

- **`intent`** — a half-page written to be forwarded, usually beside a plan.
  **It folds into the plan** as an `# Intent (forwardable)` section;
  `bin/handoff` reuses that section verbatim as every handoff's opening, so
  the two cannot drift. It is not a node, and nothing in this brain reads a
  separate one. *This was the one answer a migrator could not find anywhere,
  and the cost was a `consent_by:` that silently stopped being read.*
- **`handoff`** — the exec-facing page. **It is a projection, not a node**:
  `bin/handoff` renders it from the plan plus an authored half under
  `plans/<initiative>/handoff/`. What gets recorded is the *event* of handing
  it over, as an `evidence` node.

Anything else: map it to one of the seven and say which, or leave it as
`evidence` with a `resource:` and flag it in your report.

## Step 2 — import what already exists

In descending order of certainty, so the most trustworthy material lands first:

- **Existing ADRs** (`docs/adr/`, `docs/decisions/`) → `bin/new decision`,
  one per ADR, `status: draft`, `resource:` the original. Keep their words; do
  not improve them.
- **`CONTEXT.md`, a glossary, a data dictionary** → seed
  `brain/knowledge-catalog/glossary.md`, including the `_Avoid_` notes, which
  are usually the most valuable part and the first thing lost.
- **A README's architecture section** → an `evidence` node, not a decision. It
  describes how things are, which is Evidence; what it does not say is *why*,
  which is what a decision is for.

The host repository's own `CLAUDE.md` and `CONTEXT.md` are the team's: read
them, do not write to them.

## Step 3 — mine the transcript history

This is the step nothing else can do, and it is usually the richest.

```bash
bin/utterances            # what the operator said, across every session
bin/utterances --repeated # said more than once — the strongest signal
```

**Use the tool. Do not read the `.jsonl` yourself.**

An answer to `AskUserQuestion` is stored as a `tool_result` block inside a
user-role envelope, **not** as message text. Walk the transcript keeping
user-role entries with string content — the obvious approach — and you drop
every decision the operator made in answer to a direct question. What you find
in their place is the **assistant's paraphrase**, in assistant text.

Quote that, cite the transcript as `resource:`, and you have manufactured an
operator decision out of the previous agent's prose with provenance that looks
sound. `bin/utterances` reads both places and labels which is which.

**The signal is not "decision-shaped sentences".** Those are noise, and an
agent hunting them will manufacture a decision record out of ordinary chat.
The signal is:

- **The same correction, made more than twice.** The operator telling Claude
  the same thing in three different sessions is a decision or a principle that
  was never written down. This is the highest-value extraction available
  anywhere in this skill — it is precisely the knowledge the brain exists to
  stop re-deriving, and nothing else in the toolchain can see it.
- **Reversals** — "actually, don't do that", "we tried that and it broke".
  Those are Evidence.
- **Standing constraints** — "never touch X", "always go through Y". Candidate
  principles.

Present candidates in batches with the quoted source, and let the operator
accept or reject each. Do not write them unaccepted.

**Quote the operator, never a summary of them.** A line tagged `[typed]` or
`[answered]` by `bin/utterances` is theirs. Anything else in the transcript is
an agent talking, including an agent accurately describing what they said —
and a `resource:` pointing at a transcript that contains only the paraphrase
is a citation that cannot be checked by the person who would notice.

## Step 4 — mine the git history

- `git log --grep='revert\|hotfix\|actually'` → candidates for
  [the running record of what the brain has caught](knowledge-catalog/reference/E0001-brain-in-practice.md), which
  `/grill-change` reads first and which otherwise starts empty.
- `CODEOWNERS` → who owns what, for `owner:` on plans and specs later.
- Directories with the most churn → where the project's real complexity is, and
  therefore where decisions are most likely to be undocumented.

## Greenfield — when there is no history to mine

Steps 2, 3 and 4 find nothing. **The live conversation is the only source of
decisions that exists**, and it is a good one: the first greenfield adoption
produced two real decisions, both lifted from chat messages, and one arrived
sideways in the answer to a different question.

Apply step 3's signal discipline to the conversation instead of the
transcripts. You are looking for the same things:

- **A constraint stated in passing.** *"We do not care about prose that will
  be consumed by agents"* is a decision with alternatives and consequences,
  dropped into an opening message as an aside.
- **An ordering the operator has already settled.** *"The goal here is to make
  this an adoptable project, then we'll use the adoptable version to make our
  own voice setup, like any other end user would"* — a real sequencing
  decision, arriving in a reply to a clarifying question about something else.
- **A thing ruled out.** Anything the operator has said no to, and why.

Quote the operator's words back when proposing each one, the way you would
quote a transcript. They are the citation.

**Do not invent a decision to make the record look fuller.** A greenfield
brain with one goal and two real decisions is in better shape than one with
eight plausible ones.

**Nothing is a sanctioned outcome — but only after step 3.** The exit below is
scoped to *this conversation*; what it gates is scoped to *the project*. Take
it without having run `bin/utterances` and you can write a true sentence and
reach a false conclusion, with nothing feeling wrong. A conversation about
*evaluating* a tool contains no decisions about the project, and a session
that produced none, **with no older transcripts holding any either**, should
say so and stop:

> Nothing in this conversation settles anything durable about the project. No
> decisions written. The first one will arrive when you make it.

Reported by the second cold adopter, whose session contained zero project
decisions while the skill told them the live conversation was a good source.
An agent told material is there will find material. That is the manufacturing
this skill forbids two paragraphs up, and forbidding it without offering an
exit is what makes it happen.

**Same-day forward references are fine.** Everything written during init
carries today's date, so the direction rule cannot fire among these nodes —
a goal may link a decision written minutes later. Do not order your writes
around it or back-date anything to force a sequence. The rule is about
*imported* nodes, which carry origin dates.

## Fields this brain does not model

A source brain will carry frontmatter keys that mean something to its own
process and nothing here — `flag:`, `consent_by:`, `production_date:`. Keeping
them is harmless and they are not read by anything, which is the trap: a field
that mattered becomes invisible without ever looking lost.

Say so explicitly in your report, naming each one. **Leave the value in
frontmatter.** This used to say the working copy belonged in the authored half
of the handoff, which meant hand-copying a date out of frontmatter into prose
generated from the same plan — a second copy of a value that already exists,
in a file regenerated from the first. That is precisely the drift the
authored-half split was built to prevent, and the advice was found to be
self-defeating by the brain it was written for.

A date the work turns on is read by the generator instead. Any plan
frontmatter key ending `_by`, `_on`, `_at`, `_date` or `deadline`, with an
ISO date for a value, renders as **Dates that matter** on an executive,
business, marketing or product handoff. So `consent_by: "2026-11-20"` on the
plan reaches the page with no second copy and nothing to keep in sync; the
authored half can then refer to "the consent date" the way prose naturally
does. Fields that are not dates, and not read by anything, are still worth
naming in your report — but do not duplicate them to make them visible.

Write it under its own heading and `bin/handoff` carries it through verbatim,
after the sections that discipline generates, in the order you wrote them. It
used to render a heading only if the text matched one of ten known section
titles *and* that discipline carried it — so an executive handoff dropped five
of eight authored sections silently, including the one holding the date the
agreement turned on. Unrecognised headings now survive, and the run names
them.

## Step 5 — report what you did and what is still guesswork

```bash
bin/lint && bin/doctor && bin/graph-build
```

Then say plainly: how many nodes were imported, how many are `draft`, what you
could not find, and what the operator should confirm first. **The gaps are the
useful half of this report** — a brain that claims to have captured a project
it has only skimmed is the failure this whole skill is shaped to avoid.
