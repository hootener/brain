# Agent skills

The adaptation layer. The engineering skills in `.claude/skills/` are written generically against
"the issue tracker" and "domain docs"; these three documents say what those mean here, so the
skills need no local paths baked into them.

* [tracker.md](tracker.md) — `brain/tasks/` as the tracker: layout, task frontmatter,
  operations, claims, the frontier
* [triage-labels.md](triage-labels.md) — the five triage roles as `status` plus a `wontfix` flag
* [domain.md](domain.md) — where a term, decision, spec, plan or research note goes
* [templates/](templates/) — the OKF files to start from

Swapping these three documents retargets the whole skill set at a different tracker without
editing a single skill.
