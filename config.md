---
title: How this brain is installed
description: "The layout this brain was installed with, recorded rather than inferred, so the tools stop guessing where the repository root is."
timestamp: "2026-11-07"
tags: [brain, config, install]
status: active
mode: template
human_fields: []
repos: []
brain_revision: ""
tracker: ""
docs: []
---

# How this brain is installed

`mode` is one of:

| mode | Means | Repository root is |
|---|---|---|
| `embedded` | the brain is a `brain/` directory inside a host repository — the default ([D0004](knowledge-catalog/decisions/D0004-placement-and-install.md)) | the brain's parent |
| `sibling` | the brain is its own repository inside a workspace folder, beside the project repos it describes | the brain itself |
| `template` | this is the unadopted template; `bin/adopt` has not run | the brain itself |

`human_fields` lists frontmatter that people read and no tool does, on
purpose. `bin/unread` reports unread fields so a value that drives a workflow
cannot sit inert — but some fields earn their place without a reader. The test
an adopter proposed, and it is a good one: **does it change how a conflicting
record is read?** They kept `decided_by` because two of their decisions
contradict each other on the facts and it is the only thing making the
founder's call outrank the engineer's inference; they dropped `owner`, one
name on six tasks, arbitrating nothing. Listing a field here is the answer to
that test, not an escape from it.

In **both** modes `.claude/skills` is linked into the brain's *parent*, because
that is the directory Claude Code is run from and the only one where the
`brain/...` prefix every skill uses resolves. In sibling mode that parent is a
plain workspace folder — **the project repos listed in `repos:` are read and
never written to**, which is the entire reason to choose sibling.

`brain_revision` is the upstream commit the generic half came from, stamped by
`bin/adopt` at install and by `bin/update` afterwards. Without it there was no
way to tell what you were running, whether an update had landed, or what it
changed — the first re-adopter had to diff two trees by hand to discover that
a file had stopped existing.

`tracker` records where issues actually live, and `docs` where the project's
existing documentation does. `bin/adopt` stubs both empty and `INIT.md`
fills them — before that they were questions it asked with nowhere to put the
answers.

`repos` lists the repository folders this brain describes. In `embedded` mode
that is the one it lives in; in `sibling` mode it is every git repository
beside the brain in the workspace, and the list is a record of what this brain
is *about* — not a list of things it may modify.

# Why this is written down rather than detected

`bin/stale` used to compute the repository root as `../..` and scan paths
beginning `brain/`, which is correct **only** when the brain is one level down
*and* the directory happens to be called `brain`. The template passed its own
checks by coincidence of its folder name. Recording the mode is what turns that
coincidence into a fact, which is [D0001](knowledge-catalog/decisions/D0001-claims-must-be-checkable.md)
applied to the tooling itself.
