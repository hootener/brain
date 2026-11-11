---
id: T0005
type: task
title: "Add bin/selftest: adopt into a throwaway host and assert the install is clean"
description: "Nothing checks that a fresh install works; twelve defects came from a real adoption and none from the template's own checks."
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

The template passes its own linter **by construction** — the same hand wrote
both — so self-checking proves almost nothing. The first real adoption found
twelve defects in a day, two of which a thirty-line script would have caught
mechanically: a `git clone` install leaving a gitlink instead of 80 tracked
files, and a fresh install producing 17 dangling links.

Named as a gap in [E0002](../../knowledge-catalog/reference/E0002-portability-findings.md)
("Skills' own tests. Nothing checks that a skill still runs after an edit")
and now evidenced in [E0001](../../knowledge-catalog/reference/E0001-brain-in-practice.md).

# Acceptance criteria

- [ ] Creates a throwaway git repo, copies the brain without `.git`, runs `bin/adopt`
- [ ] Asserts `bin/lint`, `bin/doctor` and `bin/stale` all report zero errors and zero warnings
- [ ] Asserts `bin/new --next` returns `D0001`, `G0001` and `T0001` — the adopter's numbering is clean
- [ ] Asserts the skills are discoverable from the host root, and that no file was written into the host beyond `.claude/skills` and the hook
- [ ] Runs in CI and fails the build

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-08 — done, and it found a regression on its first run

`bin/selftest`: 29 checks, two layers.

**Install** — a throwaway git host, the brain copied without `.git`, `adopt`,
then lint/doctor/stale/graph-build clean, numbering free at `G0001`/`D0001`/
`T0001`, skills discoverable and resolving into the brain, and nothing written
into the host beyond `.claude/skills` and the hook.

**Foreign content** — twelve files deliberately not written for this brain,
each one something a real project actually did: a decision with no
frontmatter, an unquoted colon in a title, an imported draft with none of our
headings, a duplicate id, a status in prose, a citation in backticks both live
and dead, a node citing one written later, and a running record that may.

**It earned its place immediately.** The first run failed: `bin/adopt`
relocated `tasks/formalize` to `example/` and left `plans/formalize` behind
pointing at it — the same bug class as the seventeen dangling links, one layer
along, introduced the same day by the plan written to test `bin/handoff`.

Also in CI, and `bin/doctor` knows the tool exists.

The caveat is in the script's own docstring: a green run locks in what three
adoptions taught us and is not evidence the brain is ready for a project it
has never met.
