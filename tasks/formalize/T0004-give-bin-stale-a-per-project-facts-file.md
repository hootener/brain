---
id: T0004
type: task
title: Give bin/stale a per-project facts file so a host project can add a count without editing Python
description: "Give bin/stale a per-project facts file so a host project can add a count without editing Python"
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

`facts()` is the right design but adding a count means editing Python, so a
host project that wants "tests" or "migrations" checked either patches the
script or goes without. Named as gap 2 in
[E0002](../../knowledge-catalog/reference/E0002-portability-findings.md).

# Acceptance criteria

- [x] A project declares a countable fact by naming a command and a noun in a config file, not in Python
- [x] A missing or malformed config leaves the two built-in facts working
- [x] `bin/stale` still exits 1 when a declared count contradicts the repository

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-11 — done, against a real failure rather than the stated gap

`brain/facts.md`, a markdown table of `| noun | command |`. Reserved by `lint`,
in `NEVER_TOUCHED` for `bin/update`, absent by default — a file nobody wrote
runs nothing.

The task was written from E0002's portability gap: *a host project that wants
"tests" or "migrations" checked either patches the script or goes without.*
What justified building it was a different and sharper failure. An adopter
wrote an `invariant:` claiming every resume PDF is at most two pages; one was
three; `bin/lint` passed and this tool reported *"Nothing contradicts the
repository"*, because `pages` was not a noun it could be taught. Verified by
reproducing exactly that: **"says 2 pages; there are 3"**, exit 1.

This is the narrow case where D0005's correction can be partly undone. An
`invariant:` is surfaceable, not checkable — except a **countable** one
measured against a number the project can produce on demand. That is the only
class of invariant a machine can verify, and claiming more would be the
overclaim D0005 was corrected for.

Three things worth knowing:

* **These commands execute when `bin/stale` runs.** Same trust model as a
  Makefile in a checked-out repo, and stated in the tool because it changes
  `bin/stale` from a reader to an executor.
* **A command that cannot run is reported, never counted as zero.** A fact
  that cannot be produced is not a fact of zero, and `widgets: exit 127`
  appears as a note rather than silently making every "0 widgets" claim true.
* **A command whose pipeline silently fails can still yield a plausible
  number** — `cat missing | wc -l` is `0`. Nothing guards that and nothing can
  in general, since zero is often a legitimate count.

One defect caught in the writing: the docstring promised that a command
containing a pipe works, while the parser split on every `|` and truncated it.
The claim was about to be false in the tool that exists to catch false claims.
Split once, and there is a check for it.
