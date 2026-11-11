---
name: to-spec
description: "Turn the current conversation into a spec and publish it to the project issue tracker: no interview, just synthesis of what you've already discussed."
disable-model-invocation: true
---

This skill takes the current conversation context and codebase understanding and produces a spec. Do NOT interview the user; just synthesize what you already know.

Specs are written to `brain/knowledge-catalog/specs/`; read `brain/knowledge-catalog/reference/agent-skills/tracker.md` and `domain.md` beside it first.

**Workspace copy.** Adapted for this workspace: the issue tracker is `brain/tasks/` (local OKF task files), domain docs live in `brain/knowledge-catalog/`, and nothing agent-related is written into the repos. Read `brain/knowledge-catalog/reference/agent-skills/tracker.md` before publishing anything.


## Process

0. **Ask the record first, and find the goal this serves.**

   ```bash
   brain/bin/relevant "<the feature, in your own words>"
   brain/bin/constraints <the paths this will touch>
   ```

   Then resolve the goal. A spec carries `serves:` pointing at a
   `knowledge-catalog/goals/` node, and that edge is what lets a later reverse
   walk terminate in Intent — without it `/spec-review` has nothing to check
   the build against ([D0005](../../knowledge-catalog/decisions/D0005-intent-layer-wiring.md)).

   If no goal fits, **that is the finding**: say so and write the goal first
   (`brain/bin/new goal "<the outcome>"`). A feature that serves no stated
   outcome is worth one sentence of conversation before it is worth a spec.
   Do not invent a goal to satisfy the field.

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the spec, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Existing seams should be preferred to new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

Check with the user that these seams match their expectations.

3. Write the spec with `brain/bin/new spec "<feature name>"`, which allocates the `S` id and scaffolds the frontmatter — never pick a number by hand. Fill in `serves:` with the goal from step 0, and `initiative:`. Create `brain/tasks/<initiative>/` if it does not exist and link the spec from its `index.md`. No triage is needed; `/to-tasks` turns it into tasks.

<spec-template>

## Problem Statement

The problem that the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories. Each user story should be in the format of:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list of user stories should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

A list of implementation decisions that were made. This can include:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Technical clarifications from the developer
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets. They may end up being outdated very quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.

## Testing Decisions

A list of testing decisions that were made. Include:

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (i.e. similar types of tests in the codebase)

## Out of Scope

A description of the things that are out of scope for this spec.

## Further Notes

Any further notes about the feature.

</spec-template>
