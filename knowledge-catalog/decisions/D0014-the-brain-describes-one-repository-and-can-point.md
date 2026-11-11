---
id: D0014
type: decision
title: "The brain describes one repository and can point outside it, never vouch for it"
description: "A finding was captured correctly and still failed to land, because the artifact it needed to change lives outside the repo. The gap is now a stated boundary rather than an accident."
timestamp: "2026-11-11"
tags: [boundaries, scope, adoption]
status: active
invariant: "No node may assert that an artifact outside the repository is current, and no tool may imply one has been checked beyond its existence."
constrains:
  - bin/lint
  - bin/stale
  - bin/unread
  - knowledge-catalog/reference/agent-skills/**
---

# Context

An adopter went looking for a finding they believed had been lost to missing
capture. It had not been lost: it was in a task log, written minutes after the
run it described. **What failed was downstream.** The finding needed to change
a voice profile at `~/.claude/voice/profile/professional.md`, outside the
repository, and nothing here crosses that boundary. The record was complete and
correct while the thing it existed to change went on being wrong for eight
hours.

Their own reading, which is why this is a decision rather than a line in the
log: *capture wasn't the failure*, and the cross-boundary gap *is a better
target than the backlog ever would have been.*

Part of the boundary is already crossed, and works. A node names an external
artifact with `resource:`, `bin/lint` errors when the target does not exist and
is silent when it does, and `bin/ancestry` and `bin/graph-build` traverse it.
Two adopters use it today. So the question was never "can the record point
outside itself" — it can — but **what the record is entitled to claim about
what it points at.**

# Decision

**The brain describes one repository.** It may point at an artifact outside
that repository and confirm the file is there. It may not assert that the file
still says what a node claims about it, and no tool will imply otherwise.

Stated where a reader meets it, in `CLAUDE.md`, because the thing that cost
eight hours was not the missing check — it was that **nobody had said the gap
existed**, so a complete record reasonably read as a landed change.

A node describing an external artifact carries the limit in its own prose: what
it claims, as of when, and that nothing verifies it is still true. That is the
same discipline D0005 was corrected into — `invariant:` is *surfaceable*, not
checkable — applied one boundary further out.

# Alternatives

**A drift tripwire.** A field carrying path and content hash, with a check that
warns when the file exists and differs, silent when absent so a teammate on
another machine is not spammed. Roughly a hundred lines, and genuinely
verifiable — it can fail, which this record rates highly.

Rejected on maintenance, not on principle. The hash is stamped by hand, so
after any deliberate edit to the external file somebody must re-stamp or the
warning fires forever. **That is the permanently-red pattern this project has
already paid for twice** — the `example/` failures an adopter watched for two
rounds and stopped reading, and `bin/unread` on a field a brain had kept
deliberately. Paths under `~/` are also machine-specific, so the artifact is
visible to one person and the node is committed for everybody. And it is
ceremony, which D0012 measured as the dominant cost of using this at all.

One failure, once, does not buy a standing obligation. If external artifacts
ever become a *first-class subject* — a brain whose main concern is agent
configuration, where skills, profiles and settings are all outside the repo by
construction — that changes the arithmetic and this should be revisited.

**Say nothing and leave it as it was.** Rejected: that is the state that cost
the eight hours. A gap nobody has named is indistinguishable from a gap nobody
has thought about.

**Forbid external references entirely.** Rejected: two adopters point outside
today, usefully, and existence checking is real verification. Removing it would
delete working behaviour to make a point.

# Consequences

The limit is now readable rather than discoverable. A node may say *"the voice
profile at `~/.claude/...` said X on 2026-11-10"* and that is an honest,
dated claim about a thing the brain cannot see; what it may not say is *"the
voice profile says X"* in the present tense, because nothing here can know.

**What this does not fix, and it is the original complaint:** a correctly
captured finding whose target is outside the repo can still fail to land. This
decision makes the gap legible; it does not close it. The instrument that would
close it is the tripwire above, and the condition for building it is a brain
where external artifacts are the subject rather than an edge case.

See [the running record of what the brain has caught](../reference/E0001-brain-in-practice.md)
and [T0011](../../tasks/formalize/T0011-decide-what-the-brain-owes-an-artifact-outside.md),
which asked the question.
