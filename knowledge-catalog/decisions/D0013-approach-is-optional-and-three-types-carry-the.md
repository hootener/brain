---
id: D0013
type: decision
title: "Approach is optional, and three types carry the record"
description: "Measured across five corpora: four have zero plans, three have zero specs, and decision/task/evidence hold 93% of all nodes."
timestamp: "2026-11-11"
tags: [model, categories, adoption]
status: active
invariant: "No tool requires a brain to have an Approach node, and no document implies one is expected."
constrains:
  - bin/lint
  - bin/ancestry
  - bin/handoff
  - knowledge-catalog/reference/agent-skills/**
---

# Context

Five brains, counted rather than recalled:

| corpus | decision | task | evidence | goal | plan | spec | principle |
|---|---|---|---|---|---|---|---|
| template | 12 | 12 | 3 | 1 | 1 | 0 | 1 |
| consulting | 25 | 9 | 30 | 1 | **0** | **0** | 0 |
| solo research | 15 | 12 | 8 | 4 | **0** | 1 | 0 |
| solo, docs | 7 | 2 | 4 | 1 | **0** | **0** | 0 |
| product team | 8 | 63 | 9 | 4 | 30 | 3 | 1 |

**Four of five have no plans at all. Three of five have no specs.** `decision`,
`task` and `evidence` are 219 of 235 nodes — 93% — and all three appear in
every corpus. The one brain that uses Approach uses it heavily, and it is the
only multi-person product team in the set.

So Approach is not weak; it is **conditional on the work having a planning
phase**. The consulting adopter put it plainly: *intent arrives from outside, as prose, at 20:51
on a Monday.* Consulting, solo research and document work go from a decision
straight to a task, and that is the normal case rather than a lapse.

The tools have been corrected one at a time as each adopter hit this —
`ancestry` terminating on a decision, `serves:` accepting one — but **nothing
says it out loud**, so three adopters each discovered independently that the
layer they were skipping was optional, and one stopped running `ancestry`
entirely because every task read `⚠ UNTERMINATED`.

# Decision

**Approach is optional.** No tool requires a plan or a spec, and no document
implies a brain is incomplete without one. `CLAUDE.md` marks the row optional;
a brain with zero plans is a correct brain.

**`decision`, `task` and `evidence` are the spine.** They are what a reader and
a tool can rely on being present. Guidance, question order and defaults are
written for them first — which `bin/which` already does, asking the binding
test and the happened test before anything else.

**All seven types stay.** In particular `spec` is kept, which reverses my own
earlier recommendation and is the part worth recording.

# Alternatives

**Delete `spec`.** I proposed this from the count — 4 nodes across 235 — and it
does not survive contact with the cost. Deletion means a migration imposed on
the two brains that have one, the removal or repointing of `/to-spec`, and an
edit to D0006, which distinguishes code review from spec review. The saving is
one line in a seven-question ladder where `spec` is already question six, so it
costs a reader almost nothing to skip.

**Rarity is not the same as uselessness**, and the table makes it easy to
confuse them. `goal` is 11 nodes and `principle` is 2, and both are load-
bearing: one goal anchors sixty tasks, and you are supposed to have few. The
honest reading of a small count is "ask what it costs to keep", not "delete".

**Collapse Approach into Intent.** Rejected: The product team's 30 plans are doing real work
and would have nowhere to go, and the distinction between *why* and *in what
order* is exactly what a plan records.

**Leave it implicit, as now.** Rejected on evidence. Three adopters each worked
it out alone, one abandoned a tool over it, and the cost of saying it is a word
in a table.

# Consequences

A brain with no Approach layer stops looking like an incomplete one. The
lineage still terminates, because a task may serve a decision directly, and
`ancestry` already distinguishes the two — a goal is an outcome the work was
asked to reach, a decision a constraint it must respect.

**What this does not fix:** `/grill-change → /to-spec → /to-tasks → /implement`
is still written as one path, and for four of five corpora the middle step has
never been taken. The skills have not been rewritten here, and that is the next
honest piece of work rather than something this decision claims to have done.

**And the count will drift.** These five brains are young — the oldest is
months, not years — and a distribution taken at month three may not be the
distribution at year two. The claim defended here is narrow: *no tool may
require Approach.* It is not a claim that nobody will ever plan.

See [the running record of what the brain has caught](../reference/E0001-brain-in-practice.md)
for the rounds this is drawn from.
