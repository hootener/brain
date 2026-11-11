---
id: D0004
type: decision
title: The brain is embedded in the repository by default, and installing it is two steps
description: "Co-versioning with the code is what makes a dated claim checkable, so embedded is the default and sibling is the supported escape; bin/adopt installs and INIT.md fills."
timestamp: "2026-11-07"
tags: [brain, portability, install]
status: amended
serves: ../goals/G0001-a-brain-others-can-adopt.md
constrains: ["bin/adopt", "bin/doctor", "bin/stale", "config.md"]
---

# Context

Adoption was `cp -R`, `ln -s`, `git init`, `lint`, `graph-build`, performed by
hand and documented in a README list that asked the operator to write a brief
and a glossary before they had anything to put in them.

Placement was never decided, only assumed: `bin/stale` hardcodes
`ROOT = ../..` and scans paths beginning `brain/`, which works **only because
the folder happens to be named `brain` and sits one level down**. The template
passes its own linter by coincidence.

# Decision

**Embedded in the version-controlled repository is the default.** The brain
lives at `<repo>/brain/` and commits with the code.

The argument is specific: co-versioning is the only mechanism that makes "this
decision was true at this commit" checkable rather than asserted. A decision
and the code it governs move in one commit, the reviewer meets it in the pull
request, and `git log` puts the why beside the what. Every alternative
substitutes vigilance, and [D0001](D0001-claims-must-be-checkable.md) is about
not relying on vigilance.

**Sibling is supported, not merely tolerated**, and is required in two cases
that will occur: the operator does not control the repository (client work, a
take-home, an outside contribution), or one brain serves several repositories.
In sibling mode **nothing is written into the project repositories at all** —
see the amendment below for what that means mechanically.

**The mode is recorded, never inferred.** `brain/config.md` is an OKF
`evidence` node carrying `mode` and `repos`, so the tools stop guessing.

**Install is two steps with different shapes**:

- **`bin/adopt`** — mechanical, no model. Symlink into `.claude/skills`, stamp
  the date placeholders, write `config.md`, relocate the template's own record
  to `example/`, `git init` when sibling, run `lint` and `graph-build`. Run
  once. It ends by printing the next step.
- **`INIT.md`** — the skill. Reads the host repository, imports existing
  ADRs and `CONTEXT.md`, mines the Claude transcript history for repeated
  corrections, and interviews for the first goal. Needs judgement, so it cannot
  be a script. Run again when a second repository is adopted.

They do not call each other. One installs, one fills; they rerun on different
schedules and fail in different ways.

**Distribution is by fork, not by copy**, so a fix upstream reaches a user as
`git pull template main` rather than never.

**The template's own record is relocated, not deleted.** `bin/adopt` moves it
to `brain/example/`, which `lint` and `graph-build` skip, and prints how to
remove it. Deleting on install takes away the worked example at the moment it
is most needed.

# Alternatives

- **Sibling by default** — rejected: it severs the brain from the commit that
  makes its claims checkable, which is the whole value.
- **Inferring the mode from the directory layout** — rejected: that is what
  `bin/stale` does today, and it is why the script works by coincidence.
- **One command doing both install and fill** — rejected: the mechanical half
  must be rerunnable and trustworthy without a model in the loop, and the
  judgement half must be rerunnable per repository.
- **Deleting the example on adopt** — rejected: see above.
- **`cp -R` distribution** — rejected: forks the user's copy permanently.

# Consequences

- `bin/stale` reads `config.md` instead of assuming `../..`, which fixes the
  coincidence.
- `bin/doctor` checks the install — symlink, placeholders, config, graph
  freshness — and `bin/lint` calls it, since lint is the gate.
- The host repository's own `CLAUDE.md` gets one pointer line, and `bin/adopt`
  **offers** rather than adds it: those files belong to the team.
- **CI is opt-in for the same reason.** `.github/workflows/` is the team's
  directory, and a brain that installs a workflow into someone's repository
  uninvited has made a change nobody asked for in the most visible place
  available. `bin/adopt` describes what the workflow checks and writes it only
  on `--with-ci` or an explicit yes. The recommendation is strong — `bin/lint`
  and `bin/stale` are what keep the record's claims checkable between reviews,
  and CI is the only place they run without being remembered — but a strong
  recommendation is still a recommendation.
- Windows still needs a copy-on-install fallback for the symlink. Not solved
  here.

# Amendment — 2026-11-07

*The decision stands. It did not say where the skills link goes in sibling
mode, and `bin/adopt` got it wrong in a way that made the mode non-functional.*

**Sibling means a workspace, not a bare pair of folders.** The brain and the
project repositories sit side by side inside a plain directory, and Claude Code
is run **from that workspace**:

```
workspace/
├── .claude/skills -> ../brain/skills
├── brain/                  # its own git repository
└── acme-api/               # the project. Never written to.
```

`bin/adopt` installed `.claude/skills` **inside the brain** in sibling mode.
That had the virtue of not touching the project, but it broke two things at
once:

1. **No skill was ever discovered.** Claude Code looks in the working
   directory's `.claude/skills` and in `~/.claude/skills`. It does not look in
   `brain/.claude/skills`, so every skill was invisible from anywhere work
   actually happens.
2. **`brain/...` did not resolve.** Every skill and every document in this
   repository refers to `brain/knowledge-catalog/…`. That prefix is only
   correct from a directory that *contains* the brain.

Both are fixed by the same move: `.claude/skills` goes in the **brain's
parent** in both modes. What the mode changes is what that parent is — the
host repository when embedded, a workspace folder when sibling.

**The project repositories are never touched in sibling mode.** Not
`.claude/`, not `.github/`, not `.git/hooks`. They are recorded in
`config.md`'s `repos:` and read. This is the whole reason the mode exists: the
project may be shared with a company or owned by someone else, and a brain
that leaves brain-specific files in it has made that someone else's problem.

Consequently, in sibling mode the brain's own git repository carries the hook
and the CI workflow — which is where they already are, so `--with-ci` has
nothing to do but tell you to enable Actions.

The cost is explicit and worth stating: **you must start Claude Code in the
workspace, not in the project folder.** `bin/adopt` prints this, and it is the
price of keeping a shared repository clean.

# Amendment — 2026-11-11

**One entry point, still two steps.** The operator now types one thing:

> read https://raw.githubusercontent.com/hootener/brain/main/INIT.md and follow it

`INIT.md` gained a Step 0 that detects whether `config.md` exists, asks the
operator where the brain should live, fetches the files and runs `bin/adopt`
for them.

**What this does not change is the half of this decision worth keeping.**
`adopt` is still mechanical with no model in it, `INIT.md` still needs
judgement, and they still rerun on different schedules — `adopt` once per
brain, `INIT.md` once per repository it describes. The sentence above that
reads *"They do not call each other"* is now false in one direction and stays
true in the other: `INIT.md` runs `adopt`, `adopt` never runs `INIT.md`.
Collapsing them into a single tool was rejected for the reason the original
decision gives, which has not stopped being true.

What was wrong was never the two steps. It was that **the operator had to know
about both**, from a README that spent two screens on `cp -R` versus `git
clone` and on which one puts a `.git` in the wrong place. That is a mechanical
problem, and the thing reading the instruction is a machine. It can perform
the copy; it could never guess the placement, which is why Step 0 asks and
does not infer.

**Placement is still recorded, never inferred.** Step 0 asks before it copies
anything, because `config.md` carries the mode and moving the files afterwards
does not move the record of what they are.
