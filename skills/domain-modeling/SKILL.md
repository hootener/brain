---
name: domain-modeling
description: Build and sharpen a project's domain model. Use when discussing codebase terminology, writing or editing a CONTEXT.md, or recording or editing an ADR.
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. This is the *active* discipline: challenging terms, inventing edge-case scenarios, and writing the glossary and decisions down the moment they crystallise. (Merely *reading* `CONTEXT.md` for vocabulary is not this skill: that's a one-line habit any skill can do. This skill is for when you're changing the model, not just consuming it.)

## File structure

This workspace is single-context, but the files live in `brain/`, never in a repo:

```
brain/knowledge-catalog/
├── glossary.md                 ← the CONTEXT.md role
└── decisions/
    ├── 0001-<slug>.md          ← ADRs, OKF `decision` files
    └── out-of-scope/           ← rejected requests (triage); see /triage
```

The host repository's own `CONTEXT.md`, `CLAUDE.md` or `docs/adr/`, where they exist, are the team's own. Read and respect them; write to them only when the user explicitly asks for a team-facing ADR. See `brain/knowledge-catalog/reference/agent-skills/domain.md`.
**Workspace copy.** Adapted for this workspace: the issue tracker is `brain/tasks/` (local OKF task files), domain docs live in `brain/knowledge-catalog/`, and nothing agent-related is written into the repos. Read `brain/knowledge-catalog/reference/agent-skills/tracker.md` before publishing anything.

## During the session

### Ask the record before proposing a term

```bash
brain/bin/relevant "<the concept>"
```

A term that contradicts a decision is not a vocabulary question, it is a
contradiction — surface it as one. This skill reads the glossary and the
decisions folder but never searched the record, so a concept already settled
under a different word looked like new ground.

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `brain/knowledge-catalog/glossary.md` (or the host repository's `CONTEXT.md`), call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account': do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible. Which is right?"

### Update the glossary inline

When a term is resolved, update `brain/knowledge-catalog/glossary.md` right there. Don't batch these up: capture them as they happen. Use the format in [CONTEXT-FORMAT.md](./CONTEXT-FORMAT.md).

The glossary should be totally devoid of implementation details. Do not treat it as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR. Use the format in [ADR-FORMAT.md](./ADR-FORMAT.md): an OKF `decision` file created with `brain/bin/new decision "<the claim>"`, which allocates the `D` id.

Give it `serves:` (the goal it pursues) and, where you can state one, an
`invariant:` — the single sentence a future change could violate — plus
`constrains:`, the paths it governs. Those two fields are what let
`brain/bin/constraints` surface this decision to someone touching that code
later; without them it can only ever be found by searching for the words it
happens to use.

### A principle is a decision with no alternatives

Occasionally what crystallises is not a choice between options but a standing
constraint: *we never put a judgement where a lookup would do.* That is a
`principle`, not a decision — `brain/bin/new principle "<the constraint>"`. It
shares the `D` sequence, because it carries the same authority.

The test is the Alternatives section. If you cannot write one honestly, you
are not choosing, you are asserting — and an assertion filed as a decision
with an invented rejected option is worse than either. Principles are **rare**:
a handful per project, constraining every decision under them. If you find
yourself writing a third this month, they are decisions.
