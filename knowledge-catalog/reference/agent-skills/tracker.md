# The tracker: brain/tasks (local OKF markdown)

Tasks live as markdown files under `brain/tasks/`, in Google's Open Knowledge Format: YAML
frontmatter plus a markdown body, linked with relative markdown links. The files are the tracker;
there is no service behind them.

The word is **task**, everywhere. "Ticket" and "issue" are the same thing said two other ways,
and a project that describes one concept three ways is the thing `/domain-modeling` exists to
catch.

## Layout

```
brain/tasks/
├── README.md
├── TODO.md                      # the loose to-do list; not a tracker
├── misc/                        # one-off tasks belonging to no initiative
│   └── T0001-<slug>.md
└── <initiative>/                # one folder per planned thing
    ├── index.md                 # ordered list of the tasks, with status
    ├── map.md                   # optional: wayfinder map
    └── T0002-<slug>.md          # one task per file
```

Ids are **global and flat** (D0003): `T0002` is one task in this brain, wherever it lives.
Allocate with `brain/bin/new task "<title>" --initiative <folder>`; never pick a number by hand.

The number does **not** encode dependency order — `blocked_by` carries that, and `brain/bin/status`
computes the frontier from it. The old convention that "a task's blockers always have lower
numbers" was enforced by nothing and broken by any renumbering. Never put more than one task in
a file.

## Task file

Template: [templates/task.md](templates/task.md). Frontmatter:

| Field | Required | Values |
|---|---|---|
| `id` | yes | `T` plus four digits, from `brain/bin/new` |
| `type` | yes | `task` |
| `title` | yes | short imperative title |
| `description` | yes | one sentence |
| `status` | yes | `Not Ready` · `Ready for Development` · `In Progress` · `Testing` · `Complete` · `Delivered` · `Superseded` |
| `initiative` | yes | the folder name |
| `blocked_by` | yes | list of relative links to blocking task files, or `[]` |
| `category` | recommended | `bug` · `enhancement` |
| `wontfix` | optional | `true` when the request was rejected; status stays as it was |
| `claimed_by` / `claimed_at` | set on claim | worker name as `ListAgents` shows it; ISO 8601 |
| `spec` | when one exists | relative link to the spec — this is the task's lineage edge, so a reverse walk cannot reach Intent without it |
| `wayfinder` | optional | `research` · `prototype` · `grilling` · `task` — set on map child tickets |
| `pr` | optional | list of PR URLs |
| `timestamp` | yes | creation date |
| `tags` | recommended | short strings |

Status meanings: **Not Ready** needs triage or more information; **Ready for Development** is
fully specified and claimable; **In Progress** is claimed; **Testing** has code pushed and is
under review; **Complete** is merged; **Delivered** is live; **Superseded** is built and then
removed — the code is in the history and nobody is going to pick it up.

## Who moves a status

Every transition has exactly one owner (D0007). A transition with no owner does not happen,
which is how `Testing → Complete` used to require the operator to ask for it by hand.

| Transition | Owner | Trigger |
|---|---|---|
| `Not Ready` → `Ready for Development` | `/triage` | an agent brief is written |
| `Ready for Development` → `In Progress` | `/implement` | the claim |
| `In Progress` → `Testing` | `/implement` | the branch is pushed |
| `Testing` → `Complete` | `/spec-review` | the reverse walk passed |
| `Complete` → `Delivered` | the commit hook | merged and deployed |
| any → `Superseded` | `/grill-change` or `/audit` | decided |

`brain/bin/reconcile` reports where a status and the repository disagree; `/audit` resolves what
it cannot judge. Neither changes an inferred status without an answer.

Body headings: `# What to build`, `# Acceptance criteria`, `# Out of scope`, `# Log`. Comments,
triage notes and agent briefs append under `# Log` as `## YYYY-MM-DD — <who>` entries, newest
last.

## Operations

- **Create an issue**: write `brain/tasks/<initiative>/NNN-<slug>.md` from the template, add it to
  the initiative `index.md`.
- **Read an issue**: read the file. References are paths; "subcontractors 003" means
  `brain/tasks/subcontractors/003-*.md`.
- **List issues**: `brain/bin/status [initiative]`, or scan frontmatter.
- **Comment**: append under `# Log`.
- **Change state**: edit `status`; see [triage-labels.md](triage-labels.md) for the role mapping.
- **Close**: set `status: Complete`, or add `wontfix: true` with a `# Log` entry giving the reason.
- **Claim**: read, confirm `Ready for Development` and no `claimed_by`, set `status: In Progress`,
  `claimed_by`, `claimed_at`, write, then **re-read to confirm**. Claim first, work second. Then
  open `brain/work/<initiative>/<worker>.md` and journal.
- **Frontier**: tasks that are `Ready for Development`, unclaimed, and whose every `blocked_by`
  target is `Complete` or `Delivered`; lowest number first. `brain/bin/status` computes it.

## When a skill says "publish to the issue tracker"

Write the task files. Specs go to `brain/knowledge-catalog/specs/` and the initiative folder links
to them.

## When a skill says "fetch the relevant ticket"

Read the task file at the given path or resolved number.

## Wayfinding operations

For the `wayfinder` skill, which plans by resolving decisions rather than by slicing a build.

- **Map**: `brain/tasks/<initiative>/map.md`, with the Destination / Notes / Decisions-so-far /
  Not-yet-specified / Out-of-scope body. `map.md` is reserved — no frontmatter required.
- **Child ticket**: a numbered task file with `wayfinder: research | prototype | grilling | task`
  in its frontmatter and the question under `# Question`.
- **Blocking**: `blocked_by`. Unblocked when every blocker is `Complete`.
- **Frontier / claim**: as above.
- **Resolve**: append the answer under `# Answer`, set `status: Complete`, then add a one-line
  pointer to the map's Decisions-so-far.
