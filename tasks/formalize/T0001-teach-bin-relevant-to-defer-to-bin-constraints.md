---
id: T0001
type: task
title: Teach bin/relevant to defer to bin/constraints once decisions declare what they govern
description: "Teach bin/relevant to defer to bin/constraints once decisions declare what they govern"
status: Complete
category: enhancement
initiative: formalize
blocked_by: []
timestamp: "2026-11-07"
tags: [brain, formalize]
---

# What to build

`bin/relevant` prints **every** decision on every call, because its own
docstring admits term matching misses the one that matters. That backstop stops
working exactly when the record grows big enough to need it: four lines at
seventeen decisions, noise at eighty.

`bin/constraints` now answers the same question by lookup instead of guess —
see [D0005](../../knowledge-catalog/decisions/D0005-intent-layer-wiring.md).
Retire the backstop once enough decisions declare `constrains:`.

# Acceptance criteria

- [x] `bin/relevant` no longer prints the full decision index unconditionally
- [x] It prints the count of decisions declaring no `constrains:`, since those can never match a diff
- [x] `/grill-change` runs both tools and the guidance in its step 1 still holds

# Out of scope

- Anything not named in the acceptance criteria above

# Log

## 2026-11-10 — done

Not retired, narrowed, and the distinction is the whole design. "Retire the
backstop once enough decisions declare `constrains:`" assumes a threshold, and
a threshold would be an inference about a corpus this tool cannot see — one
adopter has 11 of 12 declaring, another 1 of 23. D0011 applies.

So it asks the question per decision instead. A decision is reachable three
ways: this search matched it, it declares `constrains:` and `bin/constraints`
will find it, or **neither** — and only the third group is printed, because
only it is a gap. The list shrinks as the record improves and empties on its
own, with no threshold to tune and nothing to retire later.

Measured both ways. On this brain, 11 of 12 declaring: four lines where twelve
titles used to be. On the consulting adopter's, 1 of 23: twenty titles, capped at twelve, each
one genuinely unreachable and the remedy attached.

Two things found while building it. The zero-hits branch returned before the
backstop ran — the one call where "what could this not surface" is the entire
answer. And `constrains:` had to be read from the raw head rather than the flat
parser, which reads a block sequence as an empty string: the same YAML bug that
once let `constrains:` govern nothing past a green lint, in a fifth parser.

Prompted by the consulting adopter measuring classification overhead down to ~5% and finding the
cost had moved to retrieval — "classification is flat per item, retrieval grows
with the record". This task's own description predicted it weeks earlier: *four
lines at seventeen decisions, noise at eighty.*
