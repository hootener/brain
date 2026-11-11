# Domain docs

Single-context. Everything lives in `brain/`. The host repository may carry its own agent-facing
files (`CLAUDE.md`, `CONTEXT.md`); those are the team's and are read, not written, unless asked.

## Before exploring, read

- `brain/knowledge-catalog/glossary.md` — the project glossary, playing the `CONTEXT.md` role.
- `brain/knowledge-catalog/decisions/` — decisions as OKF `decision` files.

If a file does not exist, proceed silently.

## Where new entries go

- A resolved term → `brain/knowledge-catalog/glossary.md`, in the `CONTEXT.md` style: term, tight
  definition, an `_Avoid_` list where a near-synonym would mislead.
- A decision → `brain/knowledge-catalog/decisions/NNNN-<slug>.md` from
  [templates/decision.md](templates/decision.md); next number after the highest present.
- A spec → `brain/knowledge-catalog/specs/<slug>.md` from [templates/spec.md](templates/spec.md).
- Research or a stack fact → `brain/knowledge-catalog/reference/<slug>.md`, `type: context`.
- A plan → `brain/plans/<YYYY-MM-DD>-<slug>.md`, `type: plan`.

## Use the glossary's vocabulary

Name domain concepts as the glossary defines them. If a concept is missing, either the language is
being invented — reconsider — or there is a gap, so add it.

## Flag conflicts

If output contradicts a decision in the catalog, say so explicitly rather than silently overriding
it. A decision that should change is **superseded** on the record, not quietly ignored.
