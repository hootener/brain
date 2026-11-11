---
name: code-review
description: "Review the changes since a fixed point (commit, branch, tag, or merge-base) for the quality of the implementation itself: correctness, security, performance, and this repo's documented coding standards plus a baseline of code smells. Runs the axes as parallel sub-agents and reports them side by side. Use when the user wants to review a branch, a PR, or work-in-progress changes."
---

Review of the diff between `HEAD` and a fixed point the user supplies, on the
quality of the code **as code**:

- **Correctness** — does it do what it says, including the edge cases?
- **Security** — what does it expose, trust, or fail to validate?
- **Performance** — what does it make slower, and at what scale?
- **Standards** — does it follow this repo's documented standards, plus the
  smell baseline below?

Each runs as a **parallel sub-agent** so they do not pollute each other's
context, then this skill aggregates.

## What this skill does *not* do

It does not ask whether the right thing was built. "Does the diff implement the
spec" and "does it still serve the intent it was cut from" are the same
question at two altitudes, they share one walk of the brain, and
[`/spec-review`](../spec-review/SKILL.md) owns both
([D0006](../../knowledge-catalog/decisions/D0006-review-split.md)).

An earlier version of this skill carried an Intent axis as a third sub-agent.
It called a command that did not exist and had never run. It is gone rather
than fixed: two implementations of one walk, kept in sync by hope, is worse
than one.

So this skill reads the diff and the repo's standards files. It touches the
brain for exactly **one** thing — `brain/knowledge-catalog/glossary.md`, so
that names in the diff match the domain's vocabulary — and works in a
repository with no brain at all if that file is absent. Proceed silently if it
is.

## Process

### 1. Pin the fixed point

Whatever the user said is the fixed point (a commit SHA, branch name, tag, `main`, `HEAD~5`, etc.). If they didn't specify one, ask for it.

Capture the diff command once: `git diff <fixed-point>...HEAD` (three-dot, so the comparison is against the merge-base). Also note the list of commits via `git log <fixed-point>..HEAD --oneline`.

Before going further, confirm the fixed point resolves (`git rev-parse <fixed-point>`) and the diff is non-empty. A bad ref or empty diff should fail here, not inside the parallel sub-agents.

### 2. Identify the standards sources

Anything in the repo that documents how code should be written, such as `CODING_STANDARDS.md` or `CONTRIBUTING.md`.

On top of whatever the repo documents, the Standards axis always carries the **smell baseline** below: a fixed set of Fowler code smells (_Refactoring_, ch.3) that applies even when a repo documents nothing. Two rules bind it:

- **The repo overrides.** A documented repo standard always wins; where it endorses something the baseline would flag, suppress the smell.
- **Always a judgement call.** Each smell is a labelled heuristic ("possible Feature Envy"), never a hard violation. Like any standard here, skip anything tooling already enforces.

Each smell reads *what it is* → *how to fix*; match it against the diff:

- **Mysterious Name**: a function, variable, or type whose name doesn't reveal what it does or holds. → rename it; if no honest name comes, the design's murky.
- **Duplicated Code**: the same logic shape appears in more than one hunk or file in the change. → extract the shared shape, call it from both.
- **Feature Envy**: a method that reaches into another object's data more than its own. → move the method onto the data it envies.
- **Data Clumps**: the same few fields or params keep travelling together (a type wanting to be born). → bundle them into one type, pass that.
- **Primitive Obsession**: a primitive or string standing in for a domain concept that deserves its own type. → give the concept its own small type.
- **Repeated Switches**: the same `switch`/`if`-cascade on the same type recurs across the change. → replace with polymorphism, or one map both sites share.
- **Shotgun Surgery**: one logical change forces scattered edits across many files in the diff. → gather what changes together into one module.
- **Divergent Change**: one file or module is edited for several unrelated reasons. → split so each module changes for one reason.
- **Speculative Generality**: abstraction, parameters, or hooks added for needs the spec doesn't have. → delete it; inline back until a real need shows.
- **Message Chains**: long `a.b().c().d()` navigation the caller shouldn't depend on. → hide the walk behind one method on the first object.
- **Middle Man**: a class or function that mostly just delegates onward. → cut it, call the real target direct.
- **Refused Bequest**: a subclass or implementer that ignores or overrides most of what it inherits. → drop the inheritance, use composition.

### 3. Spawn the sub-agents in parallel

**Standards sub-agent prompt** should include:

- The full diff command and commit list.
- The list of standards-source files you found in step 2, **plus the smell baseline from step 2** pasted in full (the sub-agent has no other access to it).
- The brief: "Report, per file/hunk where relevant, (a) every place the diff violates a documented standard: cite the standard (file + the rule); and (b) any baseline smell you spot: name it and quote the hunk. Distinguish hard violations from judgement calls: documented-standard breaches can be hard, but baseline smells are always judgement calls, and a documented repo standard overrides the baseline. Skip anything tooling enforces. Under 400 words."

**Correctness sub-agent prompt** should include the diff command and commit list, and the brief: "Report logic that is wrong or incomplete: off-by-one and boundary errors, unhandled error paths, null/empty/overflow cases, race conditions, resource leaks, and behaviour that contradicts the function's own name or docstring. For each, give the concrete input or state that produces the wrong result. Under 400 words."

**Security sub-agent prompt** should include the diff command and commit list, and the brief: "Report what this change exposes or trusts: unvalidated input reaching a sink, authz checks that are missing or applied after the effect, secrets or tokens in code or logs, injection paths (SQL, shell, template, path traversal), unsafe deserialization, and dependency or permission changes that widen the blast radius. Name the attacker and what they get. Under 400 words."

**Performance sub-agent prompt** should include the diff command and commit list, and the brief: "Report what this makes slower and at what scale: queries inside loops, N+1 access patterns, unbounded result sets or allocations, repeated work that could be hoisted or cached, and newly synchronous work on a hot path. Give the input size at which it starts to matter. Say nothing about micro-optimisations the profiler would not notice. Under 400 words."

### 4. Aggregate

Present each report under its own heading — `## Correctness`, `## Security`,
`## Performance`, `## Standards` — verbatim or lightly cleaned. Do **not** merge
or rerank findings across axes (see _Why separate axes_).

End with a one-line summary per axis: total findings, and the worst issue
_within that axis_. Don't pick a single winner across axes: that is the
reranking the separation exists to prevent.

## Why separate axes

A change can pass one and fail another, and a single reviewer holding all four
at once reliably lets the loud one mask the quiet one:

- Code that follows every convention and is wrong → **Standards pass,
  Correctness fail.**
- Code that is correct for the input it was tested with and trusts input it
  was not → **Correctness pass, Security fail.**
- Code that is correct and safe and does a query per row → **Security pass,
  Performance fail.**

Reporting them separately, from sub-agents that never saw each other's
context, is what stops one axis from absorbing another.
