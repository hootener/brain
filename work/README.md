# work

Append-only journals, one file per worker per initiative: `<initiative>/<worker>.md`.

A journal is the record of what a worker actually did while a claim was held — what was tried,
what failed, what the next session needs to know. Never edit another worker's journal. Progress
views come from `brain/bin/status`, not from a shared file.

A claim with no journal entry in the last hour is treated as stale and may be reclaimed.
