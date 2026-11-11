---
name: to-tasks
description: Break a plan, spec, or the current conversation into a set of tracer-bullet tickets, each declaring its blocking edges, published to the configured tracker (edges as text in one file per ticket locally, or native blocking links on a real tracker).
disable-model-invocation: true
---

# To Tasks

Break a plan, spec, or conversation into a set of **tickets**: tracer-bullet vertical slices, each declaring the tickets that **block** it.

The issue tracker is `brain/tasks/`; read `brain/knowledge-catalog/reference/agent-skills/tracker.md` and `triage-labels.md` beside it before publishing.

**Workspace copy.** Adapted for this workspace: the issue tracker is `brain/tasks/` (local OKF task files), domain docs live in `brain/knowledge-catalog/`, and nothing agent-related is written into the repos. Read `brain/knowledge-catalog/reference/agent-skills/tracker.md` before publishing anything.


## Process

### 1. Gather context

Work from whatever is already in the conversation context. If the user passes a reference (a spec path, an issue number or URL) as an argument, fetch it and read its full body and comments.

### 1b. Read the intent before cutting anything

**If you are working from a spec or a plan**, walk it back:

```bash
brain/bin/ancestry <the spec or plan id> --claims
```

**If you are working from the conversation** — no spec, which is the usual case
where intent arrives from outside as prose — the intent is still on record and
you still have to read it. The route is different, not optional:

```bash
brain/bin/relevant "<the change, in their words>"
brain/bin/constraints <the paths this will touch>
```

This step used to exist only in the spec form, so for a brain with no Approach
layer there was no instruction at all and the discipline it exists for — read
the intent before cutting tasks — silently did not happen. Four of five brains
measured have never written a spec (D0013), so that was the majority case.

Tickets cut without reading the goal and decisions are tickets that
re-litigate settled questions. If a decision constrains the area, the ticket
that touches it **names it in its body** — a markdown link, in prose, under
`# What to build`. That link is a lineage edge: it is how a later
`brain/bin/ancestry` reaches the decision from the task, and skipping it is
what left the Intent layer unreachable in the first place
([D0005](../../knowledge-catalog/decisions/D0005-intent-layer-wiring.md)).

### 2. Explore the codebase (optional)

If you have not already explored the codebase, do so to understand the current state of the code. Ticket titles and descriptions should use the project's domain glossary vocabulary, and respect ADRs in the area you're touching.

Look for opportunities to prefactor the code to make the implementation easier. "Make the change easy, then make the easy change."

### 3. Draft vertical slices

Break the work into **tracer bullet** tickets.

<vertical-slice-rules>

- Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests): vertical, NOT a horizontal slice of one layer
- A completed slice is demoable or verifiable on its own
- Each slice is sized to fit in a single fresh context window
- Any prefactoring should be done first

</vertical-slice-rules>

Give each ticket its **blocking edges**: the other tickets that must complete before it can start. A ticket with no blockers can start immediately.

**Wide refactors are the exception to vertical slicing.** A **wide refactor** is one mechanical change (rename a column, retype a shared symbol) whose **blast radius** fans across the whole codebase, so a single edit breaks thousands of call sites at once and no vertical slice can land green. Don't force it into a tracer bullet; sequence it as **expand–contract**. First expand: add the new form beside the old so nothing breaks. Then migrate the call sites over in batches sized by blast radius (per package, per directory), each batch its own ticket blocked by the expand, keeping CI green batch to batch because the old form still exists. Finally contract: delete the old form once no caller remains, in a ticket blocked by every migrate batch. When even the batches can't stay green alone, keep the sequence but let them share an integration branch that all block a final integrate-and-verify ticket; green is promised only there.

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each ticket, show:

- **Title**: short descriptive name
- **Blocked by**: which other tickets (if any) must complete first
- **What it delivers**: the end-to-end behaviour this ticket makes work

Ask the user:

- Does the granularity feel right? (too coarse / too fine)
- Are the blocking edges correct: does each ticket only depend on tickets that genuinely gate it?
- Should any tickets be merged or split further?

Iterate until the user approves the breakdown.

### 5. Publish the tickets to the configured tracker

Publish the approved tickets as task files:

- One file per ticket, created with `brain/bin/new task "<title>" --initiative <folder>`, which allocates the global `T` id (D0003) and scaffolds the frontmatter. Never pick a number by hand, and never put two tickets in one file. Ids are global, so the number does **not** encode position in the initiative — `blocked_by` carries the ordering.
- `blocked_by` holds relative links to the blocking task files, or `[]`.
- `status: Ready for Development` unless instructed otherwise (the tickets are agent-grabbable by construction); `spec` links the spec in `brain/knowledge-catalog/specs/` when one exists, and `serves` names the decision when one does not — **one of the two is always present**, because it is the task's typed lineage edge. A markdown link in the body also works and `bin/ancestry` follows it, but it is the weaker form: `bin/lint` treats a dangling typed edge as an error and a dangling body link as a warning, and a typed edge is enumerable where a body link is a hope. Delete whichever of the two lines does not apply; `repos` lists the repos touched.
- Append each ticket to the initiative's `index.md` (create it if missing: a numbered list of links with status).
- If the initiative folder does not exist, create it. Reuse an existing folder when the work belongs to a planned initiative already there.

Work the **frontier**: any ticket whose blockers are all done. For a purely linear chain that means top to bottom.

Do NOT close or modify any parent issue.

<task-file-template>

```markdown
---
id: T0002
type: task
title: <Ticket title>
description: <one sentence>
status: Ready for Development
category: enhancement
initiative: <folder>
repos: [<repo folder names>]
blocked_by: [./T0001-<slug>.md]
# The lineage edge. One of these, not neither:
#   spec:   when the ticket was cut from a spec
#   serves: when it was cut from a conversation — name the decision it binds to
spec: ../../knowledge-catalog/specs/S0001-<slug>.md
serves: ../../knowledge-catalog/decisions/D0001-<slug>.md
timestamp: "<YYYY-MM-DD>"
tags: []
---

# What to build

The end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

# Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

# Out of scope

- …

# Log
```

</task-file-template>

Avoid specific file paths or code snippets: they go stale fast. Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it and note briefly that it came from a prototype. Trim to the decision-rich parts, not a working demo, just the important bits.
