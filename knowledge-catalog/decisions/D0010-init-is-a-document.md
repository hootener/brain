---
id: D0010
type: decision
title: "The one thing you run before the skills load is a document, not a skill"
description: "brain-init becomes brain/INIT.md: it is the only skill needed in the session that installs the brain, and in three adoptions it never once loaded in time."
timestamp: "2026-11-08"
tags: [brain, install, skills, distribution]
status: active
serves: ../goals/G0001-a-brain-others-can-adopt.md
invariant: "Nothing the installing session must run is delivered as a skill."
constrains: ["bin/adopt", "INIT.md", "skills/", "README.md"]
---

# Context

Claude Code discovers skills when a session **starts**. `bin/adopt` creates
`.claude/skills` mid-session and then tells you to run `INIT.md`.

Three cold adoptions, three failures, at a rate of three in three:

| Round | What happened |
|---|---|
| 1 | `Unknown skill: brain-init`. Self-healed several tool calls later, which the adopter noted was *worse* — an agent that retries after doing something else succeeds and never learns the rule. |
| 2 | Unknown for the **whole session**, while the skill list refreshed mid-run and advertised it. The adopter ran it by `cat`-ing `SKILL.md`, in direct contradiction of *"never use the Read tool on skill files"*. |
| 3 | Same again. *"`INIT.md` itself is still untested after three rounds (unreachable from the installing session)."* |

The instruction `adopt` prints is **restart Claude Code**, and the second
adopter named why that is no instruction at all: *"Since the operator is the
Claude Code session, 'restart Claude Code' has no exit."* An agent cannot
restart itself. We told it to do something it cannot do, three times.

This ticket was raised proposing a Claude Code plugin, because a plugin
installs skills at user scope and would move the restart to a harmless moment
before any brain exists.

# Decision

**`INIT.md` stops being a skill and becomes `brain/INIT.md`.** `bin/adopt`
ends by naming that file, and the installing session reads it and follows it —
which is exactly what all three adopters did, successfully, after the skill
failed to load.

**No plugin, for now.** The blocker it was raised to fix affects exactly one
skill, and that skill is the only one in the set that must run *in the session
that installs the brain*. Every other skill is used in later sessions, where
discovery works and has never been reported as a problem.

Reading `INIT.md` contradicts nothing: it is a document. Reading
`skillsINIT.md/SKILL.md` contradicted a standing instruction, and an
adopter had to break that instruction to proceed.

# Alternatives

- **Ship the skills as a Claude Code plugin** — rejected *for this problem*,
  not on its merits. Verified feasible: a repository carrying
  `.claude-plugin/plugin.json` and `marketplace.json` is its own marketplace.
  Its real advantage is that installation becomes a **human** action — two
  slash commands — so the human restarts naturally where an agent cannot. But
  it is a large change (manifests, a new adopt skill, fourteen skills needing
  a "no brain here" guard, release discipline) aimed at a blocker that one
  file rename removes. Distribution and updates are a separate question, and
  nobody has yet hit those as a problem.
- **Keep telling people to restart** — rejected: it is the status quo and it
  failed three times out of three. An instruction the reader cannot carry out
  is not an instruction.
- **Make every skill a document** — rejected: the other thirteen load fine,
  and being invokable is most of their value.
- **Have `adopt` refuse to run inside Claude Code** — rejected: that is where
  people adopt from, and the one place we know they are.

# Consequences

- The skill count drops to thirteen.
- `INIT.md` is no longer invokable. It was only ever run once per
  repository, so the loss is near-total in name and near-zero in practice.
- `bin/adopt`'s restart warning goes, because nothing it recommends now needs
  one.
- The plugin question stays open and is now **only** about distribution and
  updates. If it is ever taken, it should be decided on those merits with
  evidence that re-copying `bin/` and `skills/` has actually become a burden.
- `bin/doctor`'s note about a freshly-made skills link stays. The other
  thirteen skills still will not load in the installing session; they are
  simply not needed there.
