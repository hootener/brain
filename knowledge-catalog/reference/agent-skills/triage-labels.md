# Triage labels

The skills speak in five canonical triage roles. There are no labels here; roles map onto the task
file's `status` field and a `wontfix` flag.

| Role | In this tracker |
|---|---|
| `needs-triage` | `status: Not Ready`, no triage notes in `# Log` yet |
| `needs-info` | `status: Not Ready`, latest `# Log` entry is a Triage Notes block with open questions |
| `ready-for-agent` | `status: Ready for Development`, latest `# Log` entry is an Agent Brief |
| `ready-for-human` | `status: Ready for Development`, `# Log` notes why it cannot be delegated |
| `wontfix` | `wontfix: true` plus a `# Log` entry giving the reason; status unchanged |

Category roles (`bug`, `enhancement`) go in the `category` field.

The remaining statuses — `In Progress`, `Testing`, `Complete`, `Delivered` — are lifecycle, not
triage. Triage never sets them.
