---
id: D0011
type: decision
title: "A change that reads the adopter's corpus needs a corpus that is not yours"
description: "Every semantic defect in four adoptions came from an inference about the shape of someone else's brain. The template satisfies all of them by construction."
timestamp: "2026-11-09"
tags: [portability, testing, adoption]
status: active
invariant: "A change that decides what something in the adopter's brain means is tested against a brain that is not this one."
constrains:
  - bin/*
  - knowledge-catalog/reference/agent-skills/**
---

# Context

Four cold adoptions produced twelve defects. Every semantic one — as opposed to
a reporting one — came from a single cause: **an inference about the shape of
the adopter's corpus.**

| the inference | the reality in one real brain |
|---|---|
| the initiative plan is called `plan.md` | `P0005-plan.md` |
| …has the lowest id | `P0001` is a phase plan |
| …is the only one carrying `serves:` | all 19 have it, because `bin/lint` asks |
| …is the most cited | a sub-feature is, by two, because tasks cite what they implement |
| `example/` is present | never installed |
| `config.md` says `mode: template` | `sibling` |
| a `D0004` citation means the template's | means theirs |

Seven inferences, seven wrong. **None is visible from here, because the
template satisfies all seven by construction** — and no amount of care writing
them helps, because the thing being assumed is the thing you cannot see. Three
of these shipped with a green gate and a commit message calling them verified.

The author is the worst available tester of portability, and not through
inattention: a test written against this brain cannot fail for the reason that
matters.

# Decision

**A change that reads something in the adopter's brain and decides what it
means is not finished until it has run against a corpus that is not this one.**
A change that reads nothing does not need that.

The question is answerable while writing the commit: *does this change read
something about the adopter's brain and decide what it means?*

Two ways to satisfy it, in order of preference:

1. **A synthetic corpus in `bin/selftest`.** `synthetic_brain()` builds a brain
   holding the template's tooling and none of its content; fixtures write the
   few nodes they mean to measure. A check reads this script's own syntax tree
   and fails if any fixture copies from `ROOT`, because the host-dependence
   mistake recurred four times — twice while fixing a previous instance.
2. **A cold adoption by someone who did not write the change.** Required when
   the inference is structural enough that a synthetic corpus would be built to
   satisfy it, which is the same failure one level up.

**Prefer replacing an inference with an explicit marker.** The reason is
sharper than "markers are better": the two fail in different ways. **An
inference fails as a confident wrong answer; a marker fails as absence, and
absence is reportable.** The plan picker was wrong three times by inference,
silently each time, and now says it is guessing when the marker is missing.

It does not *discharge* this decision, and saying so earlier overclaimed. the product team
measured it in their own brain: one of four initiatives is marked, two
multi-plan initiatives still fall through to inference, and for one the
inference happens to be right by luck. **A marker only helps where someone has
set it**, so the inference stays on the path until they do — which means the
fallback's honesty about guessing is doing the real work, not the marker.

# Alternatives

**Test every change against a foreign corpus.** Rejected as the cost without
the benefit: the re-exec fix, the skip reasons and the dates block read nothing
about anybody's corpus, and a rule that fires on every change is one that gets
skipped on the change that needed it.

**Trust the gates.** Rejected on evidence. `bin/lint` and `bin/doctor` were
clean and sound through all twelve defects, including a release where
`--dry-run` wrote 43 files. They check the shape of the record, not whether the
tools do what their authors say.

**Automate the judgement.** Rejected, with a line drawn. `bin/prove` catches
seven of the twelve — every defect that was stdout disagreeing with the
filesystem. It cannot catch the other five, and the boundary is sharp: it does
not know which plan a handoff should name, whether a page reads well for an
executive, or that a test is measuring its host. Those need a reader who is not
the author. That is not a gap to close; it is what review is.

# Consequences

Changes get labelled as they are written, and the ones that read a corpus wait
for one. `synthetic_brain()` carries the cost of the common case, so the label
usually means "add a fixture", not "find a volunteer".

The uncomfortable consequence is that **this decision cannot be enforced by
this repository.** Nothing here can tell whether a change inferred something,
and the `bin/selftest` guard only catches the narrow case of a fixture reading
`ROOT`. It is a question asked in review, honoured or not by the person
answering it.

Asked whether that makes it a review checklist line pretending to be a
constant, the adopter found a better answer than the philosophical one: the
decision could not reach the code it governs. `knowledge-catalog/decisions/` is
in the never-touched half of the split, so `bin/constraints bin/handoff` in an
adopted brain reported *"no decision declares that it governs these paths"* —
**the one part of the record that could not ship with the code was the part
about the code.** The invariants of decisions constraining generic paths now
ship as `docs/upstream-invariants.md`, regenerated by `bin/index` and read by
`bin/constraints` after the local catalog. An unenforceable decision that
cannot even be *consulted* is a checklist line; one a tool surfaces against the
diff is at least a constant somebody has to answer for.

See [the running record of what the brain has caught](../reference/E0001-brain-in-practice.md)
for the four rounds this is drawn from, and
[the principle that claims must be checkable](D0001-claims-must-be-checkable.md),
of which this is the portability case.
