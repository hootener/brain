---
type: glossary
title: Glossary
description: "Shared vocabulary for this project."
timestamp: "<YYYY-MM-DD>"
tags: [glossary]
status: active
---

# Glossary

The vocabulary this project is described in. If a concept is missing, either the language is
being invented — reconsider — or there is a real gap, so add it. Each term: a tight definition,
and an `_Avoid_` list where a near-synonym would mislead.

## Task

One unit of work, one file under `brain/tasks/<initiative>/`, with an id like `T0042`. The only
node type in the **Action** category, and the only one that is executable.

_Avoid_: **ticket**, **issue**, **story**, **card**. All four name the same thing, and the type
is `task`, the folder is `tasks/`, the template is `task.md`. One concept, one word.

## Frontier

Tracker term. The tasks that are `Ready for Development`, unclaimed, and whose every blocker is
done. `brain/bin/status` computes it.

## Lineage

The chain from a task back to the intent that asked for it: task → spec → goal, with decisions
constraining the route. `brain/bin/ancestry` walks it. A lineage that does not reach a goal or a
principle is **unterminated**, which means work shipped with no recorded intent.

_Avoid_: **provenance** (which is about where a fact came from — that is `resource:` on an
evidence node), **history** (which is git's).

## Drift vs omission

**Drift** is building something subtly different from what was asked. **Omission** is never
building part of it. The reverse walk finds drift; only the forward walk
(`brain/bin/ancestry <id> --forward`) finds omission, and a green test suite hides it best.
