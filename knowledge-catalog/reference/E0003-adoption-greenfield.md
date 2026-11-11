---
id: E0003
type: evidence
title: "Adoption feedback: greenfield embedded install, 2026-11-08"
description: "Nine findings from a session that did not build this brain adopting it into an empty project, each with the command that reproduces it."
timestamp: "2026-11-08"
tags: [brain, adoption, feedback]
status: active
---

# Brain adoption feedback — greenfield embedded install

**From**: a Claude Opus 5 / Claude Code session adopting the brain into a brand-new,
empty, non-git repository on 2026-11-08. The raw transcript is not
committed, per the rule that feedback is evidence and its raw material is not
(`feedback/README.md`), so this node is the record of it.

**Installed from**: upstream `98bb876`, which was `origin/main` HEAD at clone time.
Two commits behind `642b933` by the time this was written. Both ids belong to
the development history, which is not published, so they resolve to nothing
here. They are kept as the record of what was installed, not as something to
look up.

**Install performed**: embedded mode, `cp -R` + `rm -rf brain/.git`, `bin/adopt --with-ci`,
`example/` kept. Then `INIT.md` (read from the file, see finding 0).

**Result**: install sound. `bin/doctor` 0/0, `bin/lint` 23 files 0 errors 0 warnings,
`bin/selftest` 29 passed, `bin/stale` clean. One goal, one decision, one principle and
a glossary extension written.

Every claim below was verified against the files; the reproducing command is included
so you can check rather than take my word for it.

---

## Verified and resolved, 2026-11-08

Every finding reproduced against `642b933` before being fixed; the reporter
installed from `98bb876`, two commits behind, so this was not assumed.

| # | What changed |
|---|---|
| 1 | `bin/index` rebuilds every folder index from the nodes present; `bin/new` calls it, appends the change-log line and rebuilds the graph. `howto.md` no longer promises what nothing did. |
| 5 | `INIT.md` branches on greenfield vs in-flight *before* step 1, and the greenfield branch mines the live conversation with step 3's signal discipline. |
| 2 | `lint` errors on an unfilled `<placeholder>` in a title, a description or a body. First version flagged ten documented command syntaxes and zero real scaffolds; backticks and fences are now skipped. |
| 3 | `bin/new` counts ids under `example/`, so a default install starts at `D0010`. `--bare` still starts at `D0001`. `bin/selftest` asserts no id is used twice anywhere. |
| 4 | `config.md` gains `tracker:` and `docs:`, stubbed by `adopt` and filled by `INIT.md`. The tracker choice is now written as a decision, since it has alternatives and a consequence. |
| 0 | `bin/doctor` reports a `.claude/skills` link made in the last ten minutes, so a lucky recovery does not hide the diagnosis. |
| 7 | The README's embedded quickstart opens with `git init`. |
| 6 | `INIT.md` states that same-day forward references among init nodes are fine and not to be worked around. |
| 8 | Fixed by 1: `bin/new` rebuilds the graph, so the staleness warning stops firing after every write. |

Two of these were only findable from outside. **Nothing wrote an index**
while two documents promised something did — the author could not see it
because the author never ran `INIT.md` by the book. And **the default
install shipped an id collision**, which `bin/selftest` passed over because it
skipped `example/` for the same reason `bin/new` did.

---

## 0. Cold start — already fixed, with a nuance your fix doesn't cover

`bin/adopt` printed "Next: run `INIT.md` in Claude Code". `INIT.md` returned
`Unknown skill: brain-init`, because Claude Code discovers skills at session start and
`.claude/skills` was created seconds earlier.

You have already fixed this in `642b933` ("The first cold-start finding: adopt tells
you to run a skill you cannot run"), which adds `⚠ Restart Claude Code before the next
step.` That text is right.

**The nuance**: it self-healed. Several tool calls later the skills appeared in the
session's available-skills list with no restart. A transient failure is worse than a
permanent one here, because an agent that retries after doing something else succeeds
and never learns the rule — so the next adopter's agent rediscovers it from scratch.

Consider having `bin/doctor` report whether `.claude/skills` postdates the current
session, so the diagnosis survives a lucky recovery.

**Side effect worth knowing**: the only way forward was `cat brain/skillsINIT.md/SKILL.md`.
That directly contradicts the `using-superpowers` instruction "Never use the Read tool on
skill files." If the brain ships as a Claude Code plugin (your `D0010` ticket), this
goes away.

---

## 1. `howto.md` promises index upkeep that nothing implements  ← fix first

`knowledge-catalog/howto.md:19`:

> The intended path is through the skills ... They handle the format, the taxonomy,
> the ids, the cross-linking and **the index upkeep**.

`CLAUDE.md`:

> Don't hand-edit an index or a log where a skill would write it.

But nothing writes them:

```console
$ grep -rl "log\.md" brain/skills/
(no hits)

$ grep -rln "goals/index\|decisions/index" brain/bin/ brain/skills/
brain/bin/adopt        # only EMPTY_INDEXES, which RESETS them to "(none yet)"
brain/bin/selftest
```

`bin/new` allocates the id and scaffolds the node, and touches no index. So a
by-the-book `INIT.md` ends with `goals/index.md` saying *"(none yet)"* and
`log.md` saying *"(nothing yet)"* while three nodes exist on disk.

The documentation forbids the only action that fixes it. I hand-edited all three
against instructions, because there was no alternative.

**Options**: `bin/new` appends the index row and a `log.md` entry — it already owns id
allocation, so this is the same mechanical job; or the docs stop promising it and say
indexes are hand-maintained.

**Related lint gap**: nothing flags a node absent from its folder index. That is a
natural rule and exactly the silent drift D0001 exists to catch — an index that
confidently says "none yet" is a stale note, which the README argues is worse than no
note at all.

---

## 2. Unfilled scaffolds pass lint clean

```console
$ brain/bin/new evidence "Placeholder lint probe"
knowledge-catalog/reference/E0001-placeholder-lint-probe.md
$ brain/bin/lint
23 files · 0 errors · 0 warnings
```

That node still contained `description: <one sentence>` in its frontmatter and
`<what you observed>` in its body. (The only warning in the run was unrelated
`graph.html` staleness — see finding 8.)

A placeholder reaching the record is an uncheckable claim wearing a node's frontmatter,
which is D0001's precise target, and the gate waves it through. An error on `<...>`
angle-bracket placeholders in frontmatter values and section bodies is a cheap regex
with real value — it would have caught me had I been lazier.

---

## 3. Id collision between `example/` and the live record

```console
$ ls brain/example/knowledge-catalog/decisions/
D0001-claims-must-be-checkable.md   ... through D0009

$ brain/bin/new decision "Dogfood the adoption path"
knowledge-catalog/decisions/D0001-dogfood-the-adoption-path.md
```

Both the README and `CLAUDE.md` state that ids are "flat and global". After a default
install there are two `D0001`s in one tree. In my record `D0001` means *"Dogfood the
adoption path"*; in `CLAUDE.md`, `howto.md`, `config.md` and five skills, `D0001` means
*"claims must be checkable"*.

Because keeping `example/` is the default, every adopter gets this on day one, and prose
citations of the form "see D0001" become ambiguous immediately.

**Options**: renumber `example/` into a reserved band (`D9001+`); have `bin/new` skip ids
present under `example/`; or state in the README that `example/` is a separate namespace.

---

## 4. `INIT.md` step 1 elicits answers with nowhere to put them

Four questions are asked. Only one has a destination.

| Q | Destination |
|---|---|
| 1. Which repositories? | Already in `config.md`, written by `adopt`. Redundant in embedded mode — and asking invites an answer that contradicts the file. |
| 2. Where do docs already live? | **Nowhere.** |
| 3. Tracker: mirror or replace? | **Nowhere.** I put it in `log.md` because nothing else fit. |
| 4. What is the project for? | A `goal` node. Works well. |

**Options**: `config.md` gains `tracker:` and `docs:` fields that `adopt` stubs and
`INIT.md` fills; or the tracker choice becomes a decision node — it is genuinely one,
with alternatives (mirror / replace) and consequences (two places to keep in step).

---

## 5. The greenfield path has no guidance, and it is a common adoption case

The skill says:

> **A greenfield brain is easy. This is for the hard case**

Then four of its five steps serve the hard case. For this adoption, steps 2, 3 and 4
were complete no-ops:

- Step 2 (import ADRs, `CONTEXT.md`, glossary) — no docs existed.
- Step 3 (mine transcript history) — the only `.jsonl` for this project path was the
  session performing the install.
- Step 4 (mine git history) — `fatal: your current branch 'main' does not have any commits yet`.

**But there was plenty to capture, and it was all in the live conversation.** Both
decisions I recorded came from the user's chat messages:

- `D0002` (human-facing prose only; agent-facing text stays plain) came from the opening
  message — *"We do not care about prose that will be consumed by agents."*
- `D0001` (build the setup path first, generate the maintainer's own voice skills by
  running it) came from one clause in a reply to a clarifying question — *"the goal here
  is to make this an adoptable project, then we'll use the adoptable version to make our
  own voice setup, like any other end user would."*

That second one is a real decision with alternatives and consequences, and it arrived
sideways in an answer to a different question. Nothing in `INIT.md` told me to look
there.

**For a greenfield project the current conversation is the only source of decisions that
exists.** Step 3's craft — "the same correction made more than twice", reversals,
standing constraints — is good, and it is all aimed at history that hasn't happened yet.
A greenfield branch that mines the live conversation, using that same signal discipline,
would have cost nothing and is where the value actually was.

---

## 6. The direction rule is inert during `INIT.md`

Everything written during init is stamped with today's date, so "nothing cites a node
newer than itself" cannot fire. I wrote `G0001` with a link to `D0001` before `D0001`
existed, and lint passed.

Not a bug — date granularity is the right call. But the skill leans hard on the
direction rule ("twenty-eight times, in the first real adoption"), which is about
*imported* nodes with origin dates. Stating that same-day forward references among init
nodes are fine and intentional would stop an agent wasting effort ordering its writes
or, worse, back-filling dates to force an order.

---

## 7. The README's embedded quickstart omits `git init`

The copy-paste block runs:

```bash
cp -R /tmp/brain-template <project>/brain
rm -rf <project>/brain/.git
cd <project>/brain
bin/adopt
```

On a brand-new project this fails, because `adopt` requires a git host in embedded mode:

> `<host>` is not a git repository. A brain is only checkable against history when it is
> versioned — `git init` it, or use `--sibling`.

That error text is good — it names both exits and the reason. But greenfield adoption
hits it every time, and one line in the README (or `adopt` offering to run `git init`)
removes the stumble entirely.

---

## 8. `graph.html` staleness nags after every write

`bin/lint` warns until `bin/graph-build` runs:

```
warn  graph.html is older than 3 file(s) it is built from (...) — run `brain/bin/graph-build`
```

Across six node writes in one init session this appeared repeatedly. The risk is
learning to ignore a warning, which is the habit D0001 is against. Having `bin/new`
rebuild, or suppressing the warning when graph staleness is the *only* finding, keeps
the signal meaningful. Low priority.

---

## What worked — don't change these

- **`bin/adopt --dry-run`.** It showed the exact `example/` relocation (14 paths) and the
  symlink before touching anything. It is why the install felt safe rather than hopeful.

- **"Copy, do not clone", with the gitlink explanation.** Explaining the failure mode —
  eighty files collapsing into one unresolvable entry, knowledge invisible to the repo
  it is meant to be versioned with — is why I got it right first time instead of reading
  a rule and guessing the reason. `bin/doctor` backstopping it is the right belt-and-braces.

- **`bin/constraints` paid off in the same session the decisions were written.** The
  README promises the return "the second time". But because `constrains:` is declared at
  authoring time, `brain/bin/constraints skills/` already returned both new decisions as
  governing the next piece of work, minutes after writing them. That is a stronger pitch
  than the README currently makes, and worth saying out loud.

- **`ancestry --forward` finding omission.** The genuinely novel capability versus every
  other knowledge tool, and "Nothing outstanding: everything this asked for has landed"
  is exactly the right phrasing.

- **`bin/selftest`'s closing line** — *"A green run locks in what three adoptions taught
  us. It is not evidence the brain is ready for a project it has never met."* Correct
  calibration, and rare. It set my expectations accurately for everything that followed.

- **The `adopt` / `INIT.md` split.** Obviously right in practice: I could confirm the
  install was mechanically sound before making a single judgment call, and the two failed
  in different ways as D0004 predicts.

- **`move_example`'s "node files, never whole directories" comment.** I would have made
  that exact mistake, and the comment explains the consequence (a missing index, a
  dangling link, seventeen warnings) rather than just stating the rule.

---

## Summary ranking

| # | Finding | Severity |
|---|---|---|
| 1 | `howto.md` promises index upkeep nothing implements; docs forbid the only fix | high |
| 5 | Greenfield path unguided; live conversation is the only decision source | high |
| 2 | Unfilled `<placeholder>` scaffolds pass lint clean | medium-high |
| 3 | `example/` id collision with the live record, by default | medium |
| 4 | `INIT.md` Q2/Q3 answers have no destination | medium |
| 0 | Cold start self-heals, so the lesson doesn't stick (fix already shipped) | low-medium |
| 7 | README quickstart omits `git init` | low |
| 6 | Direction rule inert during init; worth documenting | low |
| 8 | `graph.html` staleness warning fatigue | low |
