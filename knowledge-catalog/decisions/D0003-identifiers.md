---
id: D0003
type: decision
title: Identifiers are a type letter and a flat global four-digit sequence
description: "Every node carries an id like D0003 — one immutable type letter, one globally unique number, no hierarchy — recorded in both the filename and the frontmatter, with uniqueness enforced by the linter."
timestamp: "2026-11-07"
tags: [brain, identifiers, lint]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "An id is unique across the brain and is never reused or renumbered except to resolve a collision."
constrains: ["bin/lint", "bin/new", "bin/renumber", "bin/ancestry"]
---

# Context

Numbering was inconsistent and ambiguous. Decisions were documented as four
digits and tasks as three. A bare number did not say what it referred to, and
`bin/ancestry` resolved one by scanning initiative folders in sort order and
**taking the first match silently** — two initiatives with an `022` yielded the
wrong lineage with no warning.

The stated invariant that "a task's blockers always have lower numbers" was
enforced by nothing and broken by any renumbering.

# Decision

An id is **one type letter plus four digits**, flat, with no hierarchy:

`G` goal · `D` decision *and* principle · `P` plan · `S` spec · `T` task · `E` evidence

- **Global for every type.** `S0001` is one spec in this brain, full stop.
  Tasks are not scoped by initiative: the initiative is a folder and a
  frontmatter field, and putting it in the id would duplicate a mutable fact.
- **Principles share the `D` sequence**, which resolves the Plan/Principle
  letter collision and states the relationship: a principle is a decision with
  no alternatives and no expiry.
- **Recorded twice**: `id:` in frontmatter and as the filename prefix
  (`D0003-identifiers.md`). The linter checks they agree. The frontmatter copy
  means a rename cannot orphan a node; the filename copy means the id is
  visible in a diff, a `git log --stat` and an editor tab.
- **Never renumbered**, except to resolve a collision, for which `bin/renumber`
  rewrites the file and every citation of it.
- `bin/new <type>` allocates, so a number is never picked by hand.

Nine thousand nine hundred and ninety-nine is not a real ceiling for a
human-maintained record. The overflow valve for tasks is a new initiative, not
a fifth digit.

# Alternatives

- **Hierarchical ids (`S0001-T0002`)** — rejected, and this was the closest
  call. The parent is mutable: retarget the task to another spec and either the
  filename changes, breaking every citation in every journal, log entry and
  commit message, or the id lies. It also duplicates the `spec:` field, so the
  two can disagree. Type is immutable, parentage is not — so type goes in the
  id and parentage stays in frontmatter.
- **ULIDs or content hashes** — rejected: collision-free and unmemorable.
  "Task 22" and `bin/ancestry 022` are load-bearing affordances, and reading a
  decision record in order is worth keeping.
- **Initiative-scoped task numbers** — rejected: `T0001` would then exist in
  five folders, which breaks both `id:` in frontmatter and the linter's
  duplicate check, the two things that make the id trustworthy.
- **Per-operator number ranges, or allocate-at-merge** — rejected as premature.
  Solo, a collision never happens; on a small team it happens a few times a
  year. Each of these costs the ordering that the sequence buys, to solve a
  problem `bin/renumber` resolves in one command.
- **Wider zero-padding as the answer to growth** — rejected: it treats a
  folder-shaped problem as a digit-shaped one.

# Consequences

- **`bin/lint` must error on a duplicate id.** This is not optional: two
  operators adding `D0014` with different slugs produce different filenames,
  so **git merges them cleanly** and nothing else would notice.
- `bin/lint` also checks id/filename agreement and the four-digit width, so
  nobody invents `D14` or `D10000`.
- Dependency order is no longer implied by a task's number. It never was
  enforced; `blocked_by` is the truth and `bin/status` reads it.
- If brains ever need to reference each other, the namespace is the brain, not
  the operator: `myproject/D0014`. Carrying `id:` in frontmatter leaves that
  door open without opening it now.
