---
name: research
description: Investigate a question against high-trust primary sources and capture the findings as an evidence node in the brain. Use when the user wants a topic researched, docs or API facts gathered, or reading legwork delegated to a background agent.
---

## First, check whether the brain already knows

```bash
brain/bin/relevant "<the question, in your own words>"
```

This skill wrote *into* the brain and never read it, so it would cheerfully
spend a background agent re-researching something `knowledge-catalog/reference/`
already answers. **"Already answered, here it is" is a valid and good result** —
report it with the path and stop, or say what the existing note does *not*
cover and research only that.

## Then spin up a background agent

So you keep working while it reads. Its job:

1. Investigate against **primary sources** — official docs, source code, specs,
   first-party APIs — not a secondary write-up of them. Follow every claim back
   to the source that owns it.
2. Write the findings to a single markdown file, citing each claim's source.
3. Save it with `brain/bin/new evidence "<title>"`, which allocates the `E`
   id and scaffolds the frontmatter. Fill in `tags`, and list each primary
   source under `# Citations`. Never inside a repo.
4. Link it from the task or decision that asked for it, if any.

**Anything measured records the measurement** — the number, its denominator and
the date — so a later reader can re-check it rather than believe it. A research
note that says "much faster" is an opinion with citations attached.

**Workspace copy.** Adapted for this workspace: the issue tracker is `brain/tasks/` (local OKF task files), domain docs live in `brain/knowledge-catalog/`, and nothing agent-related is written into the repos. Read `brain/knowledge-catalog/reference/agent-skills/tracker.md` before publishing anything.
