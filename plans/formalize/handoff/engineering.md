<!-- The authored half. bin/handoff never writes here.
     Headings must match the section titles the generator uses. -->

# The first hour

Clone it, copy it into a throwaway git repo without its `.git`, and run
`bin/adopt`. If `bin/lint` and `bin/doctor` are not both clean on that fresh
install, stop — that is the bug, and it has been the bug twice.

Then read `knowledge-catalog/decisions/` in order. `D0001` is the principle
everything else serves; `D0002` is the taxonomy; `D0003` is why ids look the
way they do. Twenty minutes, and nothing else will make sense without them.

# Traps that cost us time

- **The template passes its own linter by construction.** The same hand wrote
  both. Self-testing proves almost nothing here; only content written for some
  other system finds real defects. Two adoptions found twelve.
- **`sed`-style patching of these scripts is treacherous.** Replacing a slice
  between two function definitions silently deleted a third that sat between
  them, and the failure surfaced two steps later as a `NameError`. Patch by
  exact anchored string, then `ast.parse` before running anything.
- **A markdown paragraph that opens with `**bold**` will hang a naive
  renderer.** The list-marker break pattern matches the leading `*`, the inner
  loop consumes nothing, and the index never advances. Require whitespace
  after the marker, and keep a guard that always consumes a line.
- **A hook invoked through a symlink does not know where it lives.**
  `dirname $0/..` resolves to `.git`. Locate the brain from
  `git rev-parse --show-toplevel`, which is what CI does.
- **Rebuilding a generated file and diffing it never works as a staleness
  check** when the generator stamps a timestamp. Compare everything except the
  stamp; `bin/graph-build --check` is the pattern.

# Verified, and not

**Verified.** A fresh install is 0 errors and 0 warnings. Both adoptions run
clean on `lint`, `doctor`, `stale` and `graph-build --check`. The direction
rule, the id-uniqueness check and the YAML hazard check have each caught a real
defect in work written by the person who wrote them.

**Not verified.** No automated test exists for any of it — `T0005` is exactly
that gap. Windows is untested; the symlink needs a privilege there and the
copy fallback has never been exercised. No brain has yet been used by two
people at once, so every claim about teams is reasoning, not evidence.

# Running it yourself

No dependencies. Every tool is stdlib Python 3 and runs from the brain root:

```
bin/lint          the gate
bin/doctor        is the install sound
bin/status        what is claimable now
bin/handoff       this document
```
