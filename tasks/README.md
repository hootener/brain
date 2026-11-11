# tasks

The tracker. One OKF `task` file per task, under one folder per initiative. Ids are global and
flat (`T0042`) and are allocated by `brain/bin/new task`; dependency order lives in `blocked_by`,
not in the number. The conventions are in
[../knowledge-catalog/reference/agent-skills/tracker.md](../knowledge-catalog/reference/agent-skills/tracker.md).

Run `brain/bin/status [initiative]` for the task table and the claimable frontier.

Initiatives:

* [formalize](formalize/index.md) — readying the brain for other people to adopt

`TODO.md` is the loose list of things to do. It is not a tracker and nothing reads it
programmatically.
