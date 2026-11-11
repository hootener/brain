---
id: T0006
type: task
title: Teach lint to check backticked prose paths, not only markdown links
description: "A project accumulated 174 path references that no check could see, and a measurement built on the same blind spot reported the opposite of the truth."
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

`LINK_RE` matches `](…)` and nothing else. A real catalog carried **174
cross-references across 59 files** as backticked prose — `` `knowledge/decisions/x.md` ``
— every one invisible to the linter and every one silently rotten after a
rename.

Worse than the rot: a survey built on the same blind spot concluded the
catalog had *"zero cross-links, a flat pile, not a graph"*, which was the
opposite of the truth. A check that cannot see a reference also cannot see
that references exist.

Be careful of false positives: a backticked string is often not a path.
Require a `/` and a known extension before resolving, and warn rather than
error.

# Acceptance criteria

- [ ] A backticked path ending in `.md` that does not resolve produces a warning
- [ ] A backticked string that is not path-shaped produces nothing
- [ ] Code fences are skipped
- [ ] Running it over a catalog with known-good prose references produces zero findings

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-08 — done, narrower than written

Shipped as `check_node_refs` in `bin/lint` plus `bin/relink`.

The task as written said "check backticked prose paths". Measured against a
real corpus, that rule produced ~300 findings and most were noise: `config.md`
and `CONTEXT.md` are file *names* in prose, not citations. Requiring the path
to carry an **id** cut it to 128 findings of which 127 were genuine — 122
lost edges and 5 rotten citations, the latter a real bug where `bin/adopt`
had repointed markdown links but not backticks.

`bin/lint` reports; `bin/relink` converts. Lint stays read-only, because a
gate that mutates is surprising.
