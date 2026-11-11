---
id: E0002
type: evidence
title: "Portability findings: lifting this brain out of the project it grew in"
description: "What had to change to stand this brain up from the reference, what the empty template at ~/Source/brain now carries, and what a drag-and-drop template still lacks."
timestamp: "2026-10-30"
tags: [brain, portability, template, meta]
status: active
---

# The question

*Written on 2026-10-30 while adopting this brain into a second project, and moved here on 2026-10-31 so the findings travel with the template.*

How far is the brain from a drag-and-drop empty template usable on every new project? This
document is the answer as of 30 October, written while doing it. The template now lives at
`~/Source/brain`, its own git repo.

# What copied unchanged

- The structure: `knowledge-catalog/{decisions,specs,reference}`, `plans/`, `tasks/`, `work/`,
  `brief/`, `bin/`.
- `howto.md`, `work/README.md`, the agent-skills adaptation layer (`tracker.md`,
  `triage-labels.md`, `index.md`) and the three templates.
- `bin/lint` and `bin/status`, byte for byte apart from a docstring line.

# What had to change

| Thing | Why | Change |
|---|---|---|
| `bin/ancestry` | hardcoded one initiative folder name | resolves a task number across every initiative folder; `<initiative>/NNN` pins one |
| `bin/graph-build` | page title hardcoded | title is the name of the folder that holds `brain/` |
| `bin/stale` | counted PHPUnit tests and adversarial scenarios from the host app; scanned `bootstrap.sh` and `experiments/` | knows decisions and tickets; host-project facts are added per project |
| `bin/relevant`, `bin/graph-build`, `bin/lint` | walked `templates/` and `skills/` as knowledge | both skipped |
| `bin/change`, `bin/pin`, `bin/security` | entirely about the host app's test harness, PHP pins and prompt-fence invariants | not ported; they are the shape of project-specific checks, not generic ones |
| `/grill-change` | cited the first project's decision numbers, counts and failure history in its prose | generic prose; project-specific pitfalls now come from [E0001](E0001-brain-in-practice.md) |
| `/spec-review`, `/interrogate` | one worked example each from the first project; a vocabulary blacklist from its domain | example made anonymous; blacklist points at the glossary's `_Avoid_` notes |
| `/domain-modeling`, `/to-tasks` (from the product team's repository) | named `backend-api` and `the product team-light` | host repo named generically |
| `reference/agent-skills/domain.md` | "the distilled code carries no agent-facing documentation" | "the host repository's own CLAUDE.md / CONTEXT.md are read, not written" |
| Skill location | reference keeps skills at `<project>/.claude/skills`, outside the brain | canonical in `brain/skills/`, symlinked |
| `goals/` | taxonomy had `goal` with nowhere to put one | `knowledge-catalog/goals/` with an index; `plan`, `goal`, `context` templates added |

# What the template still lacks

1. **A one-command install.** Today it is `cp -R`, `ln -s`, `git init`, `lint`, `graph-build`.
   A `bin/adopt <project>` that does those and rewrites the `<YYYY-MM-DD>` placeholders would
   make it literally drag-and-drop.
2. **Host-project hooks in `stale`.** The design is right (a `facts()` list) but adding a fact
   means editing Python. A small `brain/stale.toml` naming a command and a noun would let a
   project add "tests" without touching the script.
3. **The docstrings still narrate the first project.** `relevant`, `ancestry`, `stale` and the
   skills explain themselves with that project's war stories. Harmless, occasionally confusing;
   tidy as they are touched.
4. **`change`, `pin`, `security` as patterns.** They are the most valuable scripts in the
   reference brain and the least portable. A `bin/README.md` describing what each defended, so a
   new project knows what to build, would carry the idea without the PHP.
5. **Windows.** The symlink needs privileges there; a copy-on-install fallback would be needed.
6. **Skills' own tests.** Nothing checks that a skill still runs after an edit. The reference
   brain had the same gap.

# What this project will test next

Whether the generic `/grill-change` is still sharp with an empty `brain-in-practice.md`, and
whether the tracker shape holds for a project whose "initiative" is a take-home with two very
different halves.
