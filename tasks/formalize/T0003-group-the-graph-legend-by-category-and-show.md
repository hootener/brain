---
id: T0003
type: task
title: Group the graph legend by category and show ids in node labels
description: "Group the graph legend by category and show ids in node labels"
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

The legend lists types alphabetically, which reads as an alphabet rather than
as the model. Grouping by the four categories of
[D0002](../../knowledge-catalog/decisions/D0002-four-categories.md) makes the
graph teach the shape the way `docs/lineage.svg` does.

Node labels show the title only, so a node seen in the graph cannot be quoted
in a commit message or a journal without opening it.

# Acceptance criteria

- [x] Legend groups type swatches under Intent, Approach, Action, Evidence, in that order
- [x] A node's label is prefixed with its id
- [x] `@intent` and the other three category names work as filters
- [x] Existing `#tag`, `@type` and `@status` filters are unchanged

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-11 — done, and checked in a browser rather than by reading

One `CATEGORY` map now feeds both the legend's grouping and the `@intent`
family of filters, so the picture and the search cannot drift apart about what
a category contains. The category name is itself a clickable filter.

`titleOf` prefixes the id and takes it out of the label budget, so a long title
truncates to make room rather than pushing the id off. Untyped files — README,
CLAUDE.md, an index — have no id and are unchanged.

**Verified by rendering it, not by reading the generator.** `file://` is not
reachable from the browser tool, so it was served over localhost and driven
directly:

| filter | hits |
|---|---|
| `@intent` | 16 — goal 2, decision 13, principle 1 |
| `@approach` | 1 |
| `@action` | 11 |
| `@evidence` | 3 |
| `@decision`, `@draft`, `#brain` | unchanged |

A sample label came back `D0001 The record is only worth…`, which is the whole
point of the second criterion.

One thing noticed and deliberately left: `spec` draws its keyboard accelerator
as `[1]`, because the letters it could claim are already taken and the
allocator falls back to digits. Pre-existing, correct, and not in scope.

**The checks added here assert generated source, not behaviour**, since the
behaviour lives in a browser the suite cannot drive. Recorded as weak in the
same terms as the skill-path checks: they catch the code being reverted, not
the picture being wrong.
