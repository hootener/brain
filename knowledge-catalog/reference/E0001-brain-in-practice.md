---
id: E0001
type: evidence
title: The brain in practice
description: "A running record of what the brain catches, costs and changes in use, appended as it happens so claims about the system can be checked rather than asserted."
timestamp: "2026-10-30"
tags: [brain, meta]
status: active
append_only: true
---

# The brain in practice

**`timestamp` is when this record was opened, not when it was last appended.**
A running record that re-dated itself on every entry would invalidate every
citation of it the moment anyone wrote a new one — the direction rule would
see a node from October citing one from November, correctly, because the
timestamp would be lying about what the file is. Each entry below carries its
own date; the frontmatter carries the file's.

Appended as findings happen. `/grill-change` reads this first: it is where the
places changes have actually gone wrong in this project accumulate.

## 2026-11-07 — the first adoption into a project already in flight

A consulting engagement with 21 dated decisions, 27 reference documents, 6
tasks and roughly 50,000 words of narrative, none of it written for this
brain. Twelve defects surfaced in a day. **Not one was found by the template's
own checks**, and the reason is structural: the template passes its own linter
by construction, because the same hand wrote both.

### What found what

Deliberate testing during development found **mechanical** bugs — and it found
them well, so this is not an argument against it:

- A five-task fixture showed `bin/ancestry` returning 8 documents of which 5
  were sideways peers, because `blocked_by` was being walked as lineage.
- The same fixture showed `--claims` surfacing 4 of 9 assertions, silently.
- Simulating the CI job showed the graph-currency step would have **failed on
  every run forever**: each build stamps a fresh timestamp onto a
  single-line payload, so a rebuild-and-diff always reports a change.
- Running the post-commit hook for the first time showed it printing nothing —
  invoked through its symlink, `dirname $0/..` resolved to `.git`, so it found
  no `bin/reconcile` and exited 0 having checked nothing. **A hook that checks
  nothing is indistinguishable from a clean repository.**
- A workspace test showed sibling mode was entirely non-functional: skills
  linked where Claude Code never looks, and the `brain/...` prefix every
  document uses unresolvable from anywhere work happens.

Adoption into a real project found a different **class** — design defects that
no amount of self-testing would reach, because they only appear when the
content was not written for the system:

- **The documented install path for the default mode was wrong.** The README
  said `git clone` into `<project>/brain`, which leaves a `.git` inside the
  brain so the host repo tracks a gitlink: measured, **1 tracked entry instead
  of 80 files**. The knowledge would have been invisible to the repository it
  exists to be versioned with.
- **A fresh install greeted its first adopter with 17 dangling links**, because
  `bin/adopt` relocated the template's own record and left everything pointing
  at it. It moved `goals/` and `decisions/` as whole directories, taking each
  `index.md` with them, and left `tasks/formalize` — this project's own
  backlog — sitting in the adopter's tracker.
- **The taxonomy was too strict for imported material.** 21 decisions written
  in the host project's own shape carried none of this brain's required body
  headings. Enforcing them on import forces a choice between mangling real
  reasoning and inventing an `# Alternatives` section to satisfy a linter —
  and the second is what D0001 forbids.
- **An invariant misfired on a real edge.** `lint`'s direction rule treated
  `blocked_by` as a citation, so a September task that discovered a November
  blocker was reported as citing a node newer than itself. `bin/ancestry`
  already drew that distinction; `lint` did not.
- **A heuristic produced five false positives on real prose.** `bin/stale`
  only looked *backwards* for words that scope a count. Every miss scoped
  itself afterwards: *"two decisions below"*, *"two decisions per email"*,
  *"750 tasks/mo"*, *"eleven numbered decisions of its own"*, *"Three
  decisions handed back"*.

### The invariants earned their keep, twice, against their own author

Porting the decisions, I set `serves: G0001` on all 21 — and the direction
rule rejected every one of them. The goal was stated **2026-11-04**; a
decision from July cannot serve a goal that did not exist. The rule caught the
exact thing it was written to forbid, in an import written by the person who
wrote the rule, where a human reviewer would almost certainly have waved it
through. The YAML hazard check caught 9 titles carrying an unquoted `: ` in
the same pass.

This is the strongest evidence so far that these checks are doing work rather
than ceremony.

### A claim this record made and had to retract

An early reading reported *"zero cross-links between the 21 decisions — a flat
pile, not a graph."* **That was wrong.** The relink pass found **174
cross-references across 59 files**. They had always existed, as backticked
prose paths that no tool could follow, and the measurement had counted only
markdown links.

The lesson is about the measurement, not the catalog: **`bin/lint` validates
`](…)` links and nothing else**, so a project can accumulate hundreds of path
references that rot silently and pass every check.

### What the brain could not represent

Three gaps that the adoption exposed and that remain open:

1. **Open questions have no node type.** 69 flagged items collapsed into a
   single 1,119-line evidence node. There is no way to say "this is an
   unresolved question with an owner" short of making it a task, which it is
   not.
2. **The status vocabulary discards the reason.** The host's statuses carried
   *why* — *"Blocked — handles frozen by Ashlee 2026-11-02 until the new site
   is done"*. The seven-value enum keeps none of that, so an
   `imported_status:` field was invented mid-port to avoid losing it.
3. **Retroactive intent has no path.** 20 of 21 decisions and 6 of 6 tasks
   have no lineage to a goal, which is true and worth knowing. But D0002
   forbids an older node citing a newer one, so a goal articulated today can
   never be named by the work that was in fact serving it. The brain can
   *detect* missing intent and offers no way to supply it after the fact.

## 2026-11-08 — the greenfield cold start, first finding

A session that did not build this brain was asked to adopt it into an empty
project, with no guidance beyond the README. It got stuck on the first step
after install.

**`bin/adopt` ends by saying "now run `INIT.md`", and you cannot.** Claude
Code discovers skills when a session *starts*, so the `.claude/skills` link
`adopt` has just made is invisible to the session that ran it. The skill does
not exist yet. Neither `adopt` nor the README said to restart.

A documentation gap, not a defect — one line in each — but a telling one:
the only person who could not have hit it is the one who wrote the install,
because they already had the skills loaded from somewhere else. This is the
same blindness as the linter passing by construction, in the first five
minutes of the adopter's experience.

**It also raises a real question about distribution.** Claude Code installs
skills natively through plugins: a repository carrying
`.claude-plugin/plugin.json` and `marketplace.json` is its own marketplace,
and `/plugin install` puts its skills in `~/.claude/plugins` at user scope.
That would not remove the restart — skills are still discovered at session
start — but it would move it to a harmless moment, *before* any brain exists,
rather than stranding the adopter mid-install.

The open conflict is that a plugin and `adopt`'s symlink would both supply the
same skills. It is a fork, not a patch, and it is recorded as such rather than
taken unilaterally in the middle of the experiment that found it.

## 2026-11-08 — four cold adoptions, and what kept coming back

Four rounds, run by sessions that did not build this brain. Round 3 was
sharper than round 2 and round 4 sharper again, against a prediction after
each that the next would thin out.

**The three classes that broke twice**, which is the finding about the
process rather than any one defect:

- **A parser.** A key with no inline value opens a YAML block sequence; five
  tools set it to `''` and dropped every item, so `constrains:` written the
  usual way governed nothing past a green gate.
- **A laundering path.** `bin/utterances` published a skill body as the
  operator's words. The first fix matched the shape a report quoted; the
  harness emits a different one, so it shipped broken and was reported again.
- **An install recipe.** The README's update block pulled a clone in `/tmp`
  and copied directories only, so `INIT.md` never arrived and an updated
  install had neither the skill nor the document. The commit that caused it
  edited that README in five other places and left the recipe untouched,
  because nothing runs a recipe.

Each came back because **the fix shipped without a check**. Across four rounds
roughly twenty code changes produced five tests, all of them written before
any adopter saw the thing. `bin/selftest` now carries a regression layer, one
named case per finding, and `howto.md` says a fix lands with its check.

**What only an outsider could see.** Three of the worst were invisible from
inside: nothing wrote a folder index while two documents promised something
did; a second `bin/adopt` swept four of an operator's decisions into
`example/` with every gate reporting green; and the update recipe was broken
by the commit that documented it. None is subtle. All three needed somebody
who walked the written path instead of the remembered one.

**What the author got wrong twice.** Telling an agent to restart Claude Code —
an instruction it cannot carry out — then making that instruction clearer
instead of removing it. The fix was to stop needing a skill before skills
load, which one file rename achieved and a plugin would not have.

### Cost

Roughly a day, of which the port itself was perhaps an hour. The rest was
fixing what the port revealed — which is the correct ratio for a first
adoption and should not be read as the steady-state cost.

## Round 6 — a verification that passed because a second bug hid the first

The worst finding of the round was not in anybody's report. It was mine.

The previous round answered the product team's critical finding — a citation rewrite that
could not tell the template's `D0004` from the adopter's, and turned 55 live
links into 404s the linter does not follow — by adding two guards:
`GENERIC_PROSE`, limiting the rewrite to the nine generic documents, and
`template_node_names()`, reading the shipped manifest so only the template's
own nodes are rewritten. Both were written. Both were committed. **Neither was
ever called.**

The commit message reported the fix as verified, and the verification was real:
a task citing the adopter's own `D0004` came back untouched. It came back
untouched because the function had a second defect — the `if new != text:`
write sat one level out from the loop over files, so only the last `.md` file
of each directory was ever written. The probe file was not last in its
directory. **A dead guard and a suppressed write, each making the other
invisible.**

What exposed it was not a new report. It was fixing the product team's finding 5 — a test
whose fixture was `copytree(ROOT)`, so it measured this brain's content rather
than its own subject. Rebuilding that fixture from a small synthetic brain
changed which file came last in the directory, the suppressed write fired, and
the assertion failed for the first time.

Three things follow, and the third is the one that generalises:

* **A green check on a fixture built from the host proves nothing about an
  adopter.** The host is the one variable a portability test cannot control.
  Fixtures are synthetic now.
* **A test that only asserts the negative cannot distinguish a working guard
  from a function that does nothing.** "The adopter's node is untouched" passes
  trivially against `def f(): pass`. Every guard now has a positive case: the
  thing it is *supposed* to rewrite is asserted rewritten.
* **Reporting a fix as verified is a claim, and it is the claim this project
  most often gets wrong.** D0001 says a claim has to be checkable. Four rounds
  running, the uncheckable one has been *my own* account of what I just fixed —
  stated in a commit message, where no gate reads it. The symptom-versus-
  mechanism pattern recorded above has a sharper form: fixing the mechanism and
  never wiring it in looks identical, from the outside, to fixing it.

Also this round, from the same report: `bin/handoff` dropped five of eight
hand-written sections from an executive page because their headings matched no
known section title — silently, so the author had no symptom to notice; it
picked the newest plan in an initiative rather than the initiative's own, and
titled a stakeholder page after a four-day-old sub-feature; and `bin/update`
announced four directories updated when one had changed, which trains a reader
to discount the report that matters.

## Round 7 — the tool that could never deliver its own fixes

The structural finding of the whole exercise, and it explains a pattern that
had been read as carelessness: **`bin/update` replaces its own file and then
keeps executing the code Python already loaded.** Every fix to its own
behaviour was therefore unobservable in the release that shipped it and only
true from the next one.

Two fixes shipped looking broken because of this — honest per-path reporting,
and re-stamping the templates — both correct on disk the entire time. The product team
proved it by running the same command twice and getting the right behaviour the
second time. It is invisible from upstream, because the template is never the
thing being updated, so no amount of care in this repository would have found
it. `bin/update` now re-execs the version it has just installed.

That accounts for two of the nine claims that read as false. It does not
account for the other one, which was the dead guard in round 6.

**The plan picker, wrong three times, each time by inference.** Last round's
rule was "the plan carrying `serves:`". It discriminated nothing, because
`bin/lint` *requires* `serves:` on every Approach node — so in any linted
brain every plan has it. A field cannot be both mandatory and a discriminator,
and the two tools had been built against each other without either noticing.
The round before that it was "the lowest id", and ids are allocated at
migration in whatever order the importer walked, which D0003 makes permanent.

The lesson is not about plans. **Each of the three rules inferred the answer
from a convention, and the convention is the thing that varies between
brains.** It now takes an explicit `initiative_plan: true`, falls back to
inbound links — real structure rather than convention — and when it has
nothing, says it is guessing and names the remedy.

**Advice that defeated itself.** `INIT.md` told a migrator to put an unmodelled
but workflow-driving value — a consent date — in the authored half of the
handoff. Following it means hand-copying a date out of frontmatter into prose
generated from the same plan: a second copy, in a file regenerated from the
first, which is exactly the drift the authored-half split exists to prevent.
The product team corrected its own earlier report to find this. The generator now reads any
`*_by` / `*_on` / `*_date` key off the plan, so the value stays in one place.

**A fixture that read the host, for the third round running — twice while
fixing the second instance.** Round 5 built a migration fixture from
`copytree(ROOT)`; round 6 replaced it with a synthetic brain and, in the same
commit, copied the template's own `D0004` into the new one. On an adopted brain
that file does not exist, so the suite raised `FileNotFoundError` after four
checks and masked about fifty — including the seven written in response to the
previous report, one of which would have caught this round's first finding
outright. A third instance (`os.remove` of `.template-record`) was still
there.

There is now a check that reads this script's own source and fails if any
fixture below `install()` touches `ROOT`. The pattern was too persistent to
leave to attention.

**Red tests that nobody reads.** Four checks needed the template's `example/`
record, which an adopted brain legitimately has none of, and they had been
reported as failures for two rounds. The product team: *"I have watched five failures for
two rounds and stopped reading them, which is precisely why a crash in the same
block took a deliberate look to notice rather than announcing itself."* A
permanently red suite is a stale note, which is D0001 applied to CI. The rule
adopted: **skip when the precondition is absent, fail when the assertion is
false** — and never reach for a skip to quiet a check that is genuinely
failing. A crashing layer is also now contained rather than aborting the run.

The suite is green on an adopted brain for the first time: 64 passed, 9
skipped, 0 failed.

### The answer to the question this project kept asking

Asked whether anything in the tooling would let an adopter catch a fix reported
as verified but not actually wired in, the product team answered no, and stated the reason
plainly: **a person is the verification layer.** `bin/lint` and `bin/doctor`
were clean and sound through every one of these defects, including the release
where `--dry-run` wrote 43 files.

That is the honest state of this template. Its gates check the shape of the
record, not whether the tools do what their authors say. Nothing built here has
closed that gap, and three rounds of trying suggest nothing built here will;
what closed it every time was somebody walking the written path in a project
the author had never seen.

## Round 8 — the adopter wrote the check the author could not

This round the headline is not a defect. Asked for the third time whether
anything an adopter could run once would have caught any of this, the product team answered
**yes, with a recipe**, and the recipe is better than anything built here in
eight rounds.

Their observation: **nearly every defect across four adoptions was the tool's
stdout disagreeing with the filesystem.** Not wrong behaviour — wrong
*reporting* about behaviour. So hash the tree around the command: assert a dry
run writes nothing, print the real run's change set beside its output for a
person to read one against the other, then run it again and assert the tree has
settled. Three assertions, one fixture-free command, no knowledge of the brain.

By their count it catches seven of twelve, **including all three the author had
reported as verified and which were not.** It is now `bin/prove`.

It found a defect on the first tool it was pointed at, and then two more:

* `bin/update` was not idempotent. Three post-copy transforms — the citation
  repoint, the template re-stamp, `config.md`'s merged values — guarantee the
  local file differs from upstream *permanently*, so `differs()` was forever
  true and the count could never reach zero. Round 7 made that number
  byte-accurate and simultaneously made it uninformative, and `(7 file(s))`
  reads like a measurement, which made it *harder* to doubt than the vaguer
  claim it replaced. It now compares upstream's bytes against a manifest of
  what it last installed.
* `bin/adopt`'s `write_config` rewrote `config.md` on every run with identical
  bytes. A write of identical bytes is still a write: it puts the file in the
  change set of a run whose output never mentions it.
* The `config.md` merge and the citation repoint were *oscillating*. The merge
  wrote upstream's body, which cites `D0004` by relative path; the repoint
  rewrote that citation to a URL; next run the two differed again. Two correct
  transforms, each undoing the other, forever.

`bin/prove` also produced its own first false positive within minutes — it
flagged two honest no-op runs for saying the same thing twice. The assertion
now looks for a report that *claims* work the filesystem does not show, which
is the actual defect shape.

**The host-dependence bug reached its fourth instance, one field wide.**
`synthetic_brain()` copied the host's `config.md`, which carries `mode:
sibling`, so the fixture was born already-adopted and the migration check
measured a brain with nothing left to migrate. The product team proved it by flipping that
single field. The guard written in round 7 could not see it, because the leak
was *inside* `synthetic_brain` and the guard only scanned below it — and the
guard was a regex, so when widened it matched its own docstring. It now walks
the syntax tree.

**The product team retracted its own suggestion, which is worth recording.** It had proposed
ranking plans by inbound citations; measuring showed the sub-feature plan has
nine and the initiative plan seven, because a plan under active work gets cited
by every task implementing it. **Activity beats structure.** The explicit
marker is right, and the retraction is why — the same discipline this record
keeps asking of its author, applied by someone else to themselves.

### What came out of it

D0011: a change that reads the adopter's corpus needs a corpus that is not
yours. Seven inferences about the shape of someone else's brain, seven wrong,
none visible from here, because the template satisfies all seven by
construction. Prefer removing an inference to testing one — a marker cannot be
wrong about a brain it has not seen, only absent, and absence is reportable.

And the honest residue, which is theirs: even with `bin/prove` and a hostile
fixture, nothing would have caught "`Not included` reads badly before the
explanation" or "this goal's `# Why` is not written for an executive". Those
are judgements about a document's audience, and the only instrument is somebody
outside reading the page. **That is not a gap in the tooling; it is what review
is.**

## Round 9 — the tool under test was its own design

`bin/prove` shipped from an adopter's recipe. They then found three defects in
it and attributed all three to themselves, which is the most useful thing a
reviewer has done in nine rounds, because the defects share one root: **the
recipe assumed `--dry-run` is universally supported and meaningful.** It is
not — 14 of the 20 tools here have none, and five of those write.

* **It was a coin flip.** Four failures in six identical attempts, because
  `graph.html` carries a second-resolution build stamp and the assertion
  depended on whether two runs straddled a second boundary. *"A check that is
  right two times in six is worse than no check, because it teaches you to
  re-run until it goes green."* The fix was already written in prove's own
  prose: assertion 2 holds a tool to "a file nobody mentioned is the finding",
  and `graph.html` *is* mentioned. Assertion 3 now applies the same standard —
  one `in out3` test, and no `--ignore` flag to maintain.
* **It accused honest tools.** `bin/prove bin/graph-build` printed *"This
  proves the tool is dishonest about what it did"* about a tool that has no dry
  run and never claimed one. It now asks `--help` whether a dry run is
  advertised, because the two ways of not having one — argparse *rejecting* the
  flag, and a hand-rolled loop *ignoring* it — are otherwise indistinguishable
  from outside.
* **And it passed vacuously, which is worse.** `bin/index` rejects the unknown
  flag and exits 2; a command that did not run writes nothing; prove reported
  `[ ok ] --dry-run changed nothing` over a deliberately stale index. One line:
  `invoke()` discarded the return code. **A false reassurance inside the tool
  built to catch false reassurances** — and, as they put it, the first clean
  case of a tool being wrong about itself.

### The decision that could not reach the code it governs

Asked whether D0011 was a review-checklist line pretending to be a constant,
they found something better than a philosophical answer and it is the
structural finding of the round. D0011 declares `constrains: bin/*`. In an
adopted brain, `bin/constraints bin/handoff` answered *"No decision declares
that it governs these paths"* — because `knowledge-catalog/decisions/` is in
the never-touched half of the split, so a decision about `bin/**` cannot travel
with the `bin/**` it governs.

**The one part of the record that could not ship with the code was the part
about the code.** They cannot ship as nodes — the ids would collide with the
adopter's own, and D0003 makes ids permanent — so the invariants now ship as
`docs/upstream-invariants.md`, regenerated by `bin/index` and read by
`bin/constraints` after the local catalog, with the adopter's own node of the
same id always winning.

They also declined to let the decision's own wording stand. "Replacing an
inference with a marker *discharges* this" overclaimed: one of four initiatives
is marked, two multi-plan initiatives still infer, and for one the inference is
right by luck. **A marker only helps where someone has set it**, so the
fallback's honesty about guessing is doing the real work. What they did accept
was the principle, for a sharper reason than the one written: an inference fails
as a confident wrong answer, a marker fails as absence, and absence is
reportable.

### The third class

Two classes had instruments — `bin/prove` for reporting honesty, D0011 for
inferences about corpus shape. The third: **nothing checks the record against
the tooling.** `flag:` on four plans, read by nothing, while a decision makes
flagging step one of the standard path. Same shape `consent_by:` had for three
rounds.

It is quiet in the worst way. Writing the field is harmless, nothing dangles,
and the author has every reason to believe the value is doing something — **a
field that mattered becomes invisible without ever looking lost.** `bin/unread`
now reports any non-structural frontmatter key that no tool or skill names, and
it is in CI.

Writing it reproduced the self-reference bug twice in ten minutes: the tool
matched its own docstring, which mentions `flag:` and `consent_by`, and then
`bin/selftest`'s fixture data satisfied the check it was testing. The fixture's
field name is now built by concatenation so the source cannot contain it.

They also corrected themselves again, unprompted: their first sweep called
`consent_by` dead, which was false by then.

## Round 10 — the loop's own answer is stop

Asked whether the loop should continue, the adopter said no, and gave the
reason that makes it credible rather than polite: **severity has fallen roughly
an order of magnitude per round.** Round 1 could destroy a 12KB change log.
Round 10's worst finding is an over-confident verdict line. And three of its
own candidate findings this round were its own measurement errors — *it is now
generating false findings at about the rate it finds real ones.*

That is the signal to stop, and it is a better one than "we ran out of
findings." The loop is not exhausted; it has become a worse instrument than the
thing it is measuring.

Two real findings landed first.

**The coin flip was never fixed, only relocated.** Round 9 exempted announced
derived files from the idempotence assertion, and the symptom went away — 0
failures in 6, from 4 in 6. But the root cause was one word: `snap()` recorded
`int(st.st_mtime)`, second resolution, so a rewrite to identical bytes inside
one second is invisible. The defect simply moved into the adjacent assertion,
where the run said *"wrote brain/graph.html: 158 nodes, 334 edges"* and four
lines later *"changed: 0 file(s) — output and filesystem agree."* `snap()`'s own
docstring already argued for nanoseconds: *"a tool that rewrites a file to
identical bytes has still written it."* It now uses `st_mtime_ns`.

**And the one that matters most: nothing validated a typed frontmatter edge.**
`P0027` in their brain carries `intent: ./intent.md`, pointing at a file its own
migration deleted. The file does not exist. `bin/lint` reported zero errors. A
deliberately bogus `spec:` edge drew no warning either — because lint validates
*body* links, while frontmatter paths were resolved only to feed the direction
rule.

This is exactly backwards from the project's own argument. The README says **"a
typed edge is enumerable; a body link is a hope"** — and the linter was checking
the hope. An unresolvable body link is a warning, because it may be marking
future work; an unresolvable typed edge is now an **error**, because a tool
traverses it. The check also validates the target's *kind*, so `spec:` pointing
at a goal is caught.

Writing that check produced three false positives inside the gate on its first
run: `constrains:` holds path *globs* resolved from the brain root, not
citations from the node's directory. Excluded entirely rather than merely
untyped.

**`bin/unread`, one round old, was right for the wrong reason.** `consent_by`
passed because a code *comment* quoted it — delete the comment and a live
consent date reads as dead. Stripping comments fixes the evidence and breaks
the answer, because that field is read by **shape**, not by name:
`bin/handoff` renders any `*_by` / `*_on` / `*_date` key, so no search for
`consent_by` can find the code that reads it.

The fix is the one D0011 already prefers: tools *declare* the shapes they read,
in a `# reads-frontmatter:` line. An inference fails as a confident wrong
answer; a declaration fails as absence. `bin/unread` also now says when some
tool iterates frontmatter wholesale, because a key reached through a loop
variable is read and no textual check can see it — a verdict it cannot support
is reported as a candidate instead.

**The instance that prompted `bin/unread` was itself wrong**, as they had
flagged: `flag:` appears in zero frontmatter in their brain and in 30 files as
prose. Their assessment is fair and worth keeping: *"the best evidence for
D0011 that exists"* — a corpus-shape assumption about someone else's brain,
made one round after writing the decision against exactly that.

### What the refusal taught

Handed a filename that already held a previous round's report, the agent
declined to write there, because the directory is gitignored and that file was
the only copy. It was right, and **nothing in the directory would have stopped
it.** `feedback/README.md` now says plainly that nothing there is recoverable.
Keeping other people's raw words out of git is a good reason; it is not a safe
one.

### Closing the loop

Ten rounds, four cold adoptions, roughly twenty defects. What found them was
never a gate: it was somebody walking the written path in a project the author
had never seen. Three classes now have instruments — `bin/prove` for a tool
lying about what it did, D0011 for inferences about a corpus you cannot see,
`bin/unread` for a field the record carries and no tool reads. The residue is
judgement about a document's audience, which stays a person's job.

The loop stops here on the adopter's recommendation, not on the author's
judgement that it is finished.

## Round 11 — a third corpus, and the first honest answer about value

The consulting corpus holds 29 evidence nodes, 23 decisions, 2 goals, 12
tasks, **zero plans and zero specs**. Four months of its own history before the
brain arrived. It produced the most useful report of the eleven, and most of
what it found was not a bug.

**The instructions could not run.** `bin/update` does not exist in an install
that old — nor `unread`, `selftest`, `prove`, `handoff`, `index` or
`utterances`. Only the tool's own docstring carried the right recipe
(`--target` from a template checkout); nothing in the onboarding path did.
**The install old enough to need updating is the one least likely to have the
tool**, and that was true for the entire population the update story exists
for. The README now leads with the bootstrap.

It also explains a miss that looked like a defect: run 2 printed "Already
current", not "bin/update itself changed". The re-exec mechanism is real and
cannot fire on a brain that had no `bin/update` to be stale.

### Two findings

**`bin/constraints` was silent about what it suppressed.** The consulting adopter owns every id
the template ships invariants for — D0002, D0003, D0005, D0007 through D0011 —
so precedence put its own nodes first, which is correct. But it reported *"no
decision declares that it governs these paths"* and never mentioned that an
upstream invariant existed and had been set aside. **Correct answer, wrong
report: a reader cannot tell a gap from a collision.** Suppressed invariants
are now named.

**And the reflex had nothing to answer with.** One of 23 decisions declared
`constrains:` — the one written that day after reading `CLAUDE.md`. So a check
`CLAUDE.md` mandates before every change returned nothing every time it was
run during real work. The tool printed the same sentence for "nobody has said"
as for a genuine no. It now reports the census when the ratio is that bad,
because a reflex that always returns nothing teaches you to stop running it.
Their phrasing for the fix — *"the `bin/unread` idea applied to edges"* — is
exactly right and is what it does.

**`bin/selftest` told the consulting adopter its brain was broken for having history.** Five
failures, all from `example/` coexisting with a live record: 14 ids overlap,
because the July port numbered by hand before `bin/new` existed. And the
template **contradicted itself** — `.template-record` says *"a brain that
already holds its own D0001 keeps it"*, and the suite called precisely that a
failure. On four months of evidence the design note is right. Ids are unique
within the live record; `example/` is a separate namespace, and the overlap is
now a note.

Three further host-dependent assertions fell out of fixing it: numbering floors
hardcoded to `G0001`/`D0009`/`T0009`, which are facts about *this* brain's
`example/`; and two checks keyed on `.template-record` when what they need is
`example/.manifest`, which `doctor` was already diagnosing correctly one line
away. That is the fifth and sixth instance of the host-dependence bug, both
inside `install()` — the one function the guard written to catch it
deliberately exempts. The consulting adopter now runs 93 passed, 0 failed, 1 skipped.

`bin/adopt --repoint-only` also printed **nothing at all** when there was
nothing to do, so an already-repointed run looked identical to a failed one.

### The model does not fit, and the fix is not more ceremony

Asked whether the empty Approach layer was a gap, they declined the leading
question: *"I'd rather tell you the model doesn't fit than add plans to satisfy
a linter. The consulting adopter is consulting. Intent arrives from outside, as prose, at 20:51
on a Monday. 29 evidence to 23 decisions to zero plans is structural, not
lazy."*

The ask was not to relax the rule but to change the right one: **let a lineage
terminate on a decision and say so.** That is correct on D0002's own terms — a
decision *is* Intent — and restricting `serves:` to a goal forced a choice
between inventing plans and living with every task `UNTERMINATED`, which is
why they stopped running `ancestry`. A decision now terminates a lineage, and
the report distinguishes the two: a goal is an outcome the work was asked to
reach, a decision a constraint it must respect.

### The test for a field no tool reads

They kept `decided_by` and dropped `owner`, and the criterion is better than
the one this project had: **does it change how a conflicting record is read?**
Two of their decisions contradict each other on the facts — packages are
family-level, decided from data; blocks are one-student, decided against that
data, knowingly — and `decided_by` is the only thing that turns that conflict
into a hierarchy. The founder's call about her own business outranks the
engineer's inference, and no tool needs to read the field for that to be true.
`owner` was one name on all six tasks and arbitrated nothing. `bin/unread` now
states the test rather than implying every unread field is dead.

### What the brain was actually worth

The first straight answer anyone has given, and it is not flattering to the
model:

> Worth it, but almost none of the value came from the parts the model is built
> around.

What carried the day was **the project log** — an append-only prose journal,
barely a typed node. It caught a 24-versus-48-hour cancellation policy before
the wrong one went out to every new family, stopped a direct PDF link that
would have silently killed acknowledgement tracking, and turned a second
mislabelled notification into "this is a pattern". `bin/relevant` was *"the
best tool per keystroke"*. The supersede discipline mattered most because they
were wrong three times in one day and the wrong versions are still visible.

Unused: `constraints` (no data), `ancestry` (unterminated on everything),
`stale`, `reconcile`, and **the task graph** — because the real unit of work
all day was "what did the client just say in email".

Ceremony runs **15–25% per change**, and it is in the wrong place: *"writing is
cheap, choosing the category is the cost and has no tool."* That is the
sharpest open problem in this record and nothing here addresses it.

## Round 12 — chasing the one cost nothing addressed

The consulting adopter's sharpest sentence was not about a defect:

> Writing is cheap, choosing the category is the cost and has no tool.

With the overhead measured at 15–25% per change, that is the largest single
finding in this record, and it had sat unaddressed because it is not a bug —
nothing was broken, the brain simply charged for a decision at the worst
possible moment and gave nothing back at the time it was paid.

Two tools, and a decision (D0012) saying why they are separate.

**`bin/note` takes a capture with no type.** One command, dated entry, no
folder, no id, no required headings. The claim is measured rather than
asserted: a fresh `bin/note` entry leaves the gate at **0 errors**, where a
fresh `bin/new evidence` node leaves it at **2** until somebody hand-edits the
scaffold prompts away. That gap is the ceremony, visible in a number.

**`bin/which` answers the type question in two questions**, ordered by how
often each answer is right — *has it already happened*, then *did you choose
between alternatives a change could violate*. A real consulting brain is 29
evidence to 23 decisions; a template brain is the opposite shape; the first
two questions settle most of both.

Neither reads your text. D0011 holds, and the stakes invert here: **a wrong
type is worse than no type, because a typed node looks decided.**

### What building it taught

**The same pattern-versus-identity mistake, caught one step before shipping.**
`bin/note` first picked its record as "the first append_only evidence node with
no initiative" — which sent a note about a client's cancellation policy into
*this brain's own record of adoption findings*, a topical journal that happens
to share the shape. The fix is the one `.template-record` already paid for: a
declared `notes: true` marker. That is the seventh instance, and the first
caught before an adopter found it.

**And the tool for zero friction nearly shipped with friction in it.** The
evidence template offers `resource: <link or URL to the original>` and a body
prompt asking for a measurement's denominator — correct for evidence, wrong
for a journal, and both are unfilled placeholders the gate rejects. A first
`bin/note` would have handed the user two lint errors from a command whose
entire purpose is not making them stop and think.

### What this does not fix

Nothing measures whether promotion happens. `bin/note --review` shows the
backlog and no gate objects to it growing — deliberately, because objecting
would reintroduce the obligation being removed. A brain that never promotes
anything degrades toward a diary with a linter.

That trade is taken on three projects' evidence that the alternative was not a
well-classified record but a **thinner** one, because capture did not happen at
all. Whether it holds needs somebody living with it for a quarter, which is the
same instrument the portability question still waits on.

## Round 13 — the overhead moved sideways, and the vindication does not count

The consulting adopter used `bin/note` in real client work and reported three things. The
second is the most important and it is a warning about reading the first.

**Classification fell to ~5%, and the cost did not go away — it moved to
retrieval.** Their diagnosis is sharper than the measurement: *"classification
is flat per item, retrieval grows with the record."* So the thing that was
fixed was the cheaper of the two, and the one now dominant gets worse the
longer the brain is used. They also noted the drop was **not caused by either
new tool** — the record had pre-framed the question, so the type was already
obvious.

Their ask was exact and is built: **`bin/relevant` returns a line number.** A
path sends a reader into a 900-line evidence node; `path:683` is the citation
this project keeps claiming it wants. Getting it right needed the frontmatter
counted — reported against the body alone the numbers were wrong by exactly the
height of the head, which is close enough to look right and land you in the
wrong paragraph. That is worse than no number.

**`bin/which`'s first question mis-sorted consulting work, and it is the same
structural mismatch as last round, reappearing in the tool built to route
around it.** Asking "has it already happened — observed, measured, or *told to
you*?" first typed a client's scope decision as evidence. A client setting a
cancellation window is something you were *told*, and it is a decision — and
`bin/constraints` only works on it because it is filed as one.

The ordering was wrong for a reason worth keeping: it was ordered by **how
often each answer is right**, which is the wrong axis. It is ordered by **what
it costs to get wrong** now. A decision filed as evidence is silent and
permanent — it never surfaces against a diff again. Evidence filed as a
decision is noisy and cheap. The binding test goes first, and no longer
presupposes that *you* were the one who chose.

### The finding about the finding

> Capture-wins is right for the consulting adopter, and that corpus is the wrong place to prove it.

An unclassified prose question became a decision node within minutes, because
the thinking was already on disk. That is real vindication of the capture half.
But the consulting adopter's only promotion mechanism is **a client answering a question**,
several times a week — an external forcing function. It is *structurally immune
to the failure mode D0012 names*, which is a backlog nobody returns to.

Their line: generalising from that corpus would be reading the one project that
cannot exhibit the risk. D0011's own logic, turned on D0012 by the adopter
rather than the author, one round after the author wrote D0011. The decision is
amended to say so: the capture half is established, the claim that leaving
entries unclassified is *safe* is not, and the instrument for it is a project
with nobody outside asking questions — solo work, or an internal codebase.

Worth stating plainly, because it is the habit this whole record has been
trying to build: **the most valuable thing in a report can be the reason not to
believe its good news.**

## Round 14 — the absence was the finding

The solo research brain ran the `bin/note` round as the corpus the consulting adopter had disqualified
itself from being: solo, internal, nothing arriving from outside to force a
promotion. Three of its four findings are defects in tools shipped in the two
preceding commits.

**A prediction missed, and the miss was worth more than a hit.** I predicted
two lint errors from unfilled scaffolds; lint reported zero. Their response was
to ask *why* the errors were absent, and the answer is that **two live tasks
carry `spec: <optional relative link to …>` right now and the gate waves them
through.** `check_placeholders` inspected only `title` and `description`;
`check_edges` filtered to values ending `.md`. **Two checks, each declining to
look where the other did not** — and the thing they both missed is the exact
class the placeholder check exists for. It now reads every frontmatter field,
with `timestamp` exempted because `bin/adopt` stamps it and `doctor` already
warns at the right severity.

**`bin/note --review` asserted a backlog that had never existed.** With no
notes record at all it reported *"every entry has been promoted or left
deliberately"* — a flattering claim about work nobody had done, from a tool
three commits old whose purpose is making claims checkable. D0001's own target.
It distinguishes the two facts now.

**And `bin/which`'s ladder was unreachable by an agent.** It fell back to the
seven-question table whenever stdin was not a tty — so the part that resolves a
type in two questions instead of seven only ever worked for a human at a
terminal, and the main caller of every tool here is an agent. They had to drive
it through a pty to test it at all. Answers can now come from argv
(`bin/which n y`) or piped stdin, and the table is the fallback when there is
genuinely nothing to read rather than the universal one.

### The finding that reshapes D0012

They had briefed themselves that a finding was lost because nobody captured it.
It had been captured — a task log entry written minutes after the run it
described. **Capture was not the failure.**

What failed was downstream, and no tool here can see it. The finding needed to
change a voice profile at `~/.claude/voice/profile/professional.md`, **outside
the repository**, where neither `bin/note` nor `bin/new` nor any gate reaches.
The record was complete, correct, and the thing it existed to change went on
being wrong for eight hours with the evidence sitting in a task log the whole
time.

So the risk D0012 was written to worry about — prose piling up unclassified —
is not the risk that materialised. There was no backlog, and the tool did not
invent one. The real exposure is **a correctly captured, correctly classified
finding whose target lives where the brain cannot look.** That is now T0011,
and the decision it needs is whether cross-boundary artifacts are in scope at
all — the honest alternative being that the brain describes one repository and
says so, rather than leaving the gap as an accident of what the tools happen to
walk.

Their own summary is the right one: *the cross-boundary gap is a better target
than the backlog ever would have been.*

**D0012 is still a decision taken on an argument.** One session's absence of
harm is not evidence of safety, and the amendment says so rather than claiming
the round as a win.

## Round 15 — the vocabulary was never in the file

G0001's third signal — *an agent given only `CLAUDE.md` places a new node in
the right category and reaches for the right skill* — was unmet, and asking
what more could be done produced a measurement rather than a guess.

**Twelve fields the toolchain reads were never named in `CLAUDE.md`:**
`serves`, `constrains`, `invariant`, `blocked_by`, `category`, `claimed_by`,
`append_only`, `wontfix`, `reviewed`, `resource`, `notes`, `human_fields`.

That single fact explains findings from all three corpora which had been
recorded separately as if unrelated:

* Three adopters invented fields — `supersedes` for a reverse supersede
  pointer, `intent:` for a node type this brain folded away, `owner` for
  ownership. All reasonable guesses by an agent that had not been told what
  existed.
* the consulting adopter carried `constrains:` on **1 of 23** decisions, so the check
  `CLAUDE.md` mandates before every change returned nothing every time it ran.
  That was filed as an adopter gap. It was a documentation gap: the file they
  were told to read never mentioned the field.
* The very first finding of this whole exercise was that the Intent layer was
  unreachable because nothing wrote `serves:`. `CLAUDE.md` never named
  `serves:` either — fifteen rounds later.

`CLAUDE.md` now carries a table of every field a tool reads, what reads it, and
**what its absence costs** — the last column being the part that makes it worth
reading, since `constrains:` missing is not a style lapse but a decision no
tool can ever surface. It also carries the two-question type test inline, so an
agent does not have to run a tool to get the discrimination that matters.

### Making the signal checkable

The mechanical half is now a check: every field named in a shipped template or
in `lint`'s own edge table must appear in `CLAUDE.md`. Both sources are real
declarations elsewhere in the toolchain, so it cannot be satisfied by editing a
list sitting next to the check. **Verified to fail** by deleting the
`constrains:` row — a guard that cannot fail is a lesson this record has paid
for more than once.

The judgement half is split out and left unmeasured on purpose. Whether a
placement is *right* cannot be checked, and the only instrument is reading what
an agent did in a session nobody coached. Three such readings exist, and all
three mis-sorted something — most often a choice that arrived as prose from
outside, filed as evidence rather than as the decision it was.

**The pattern worth keeping:** the signal was not failing because agents were
careless or because the taxonomy is too hard. It was failing because the one
file an adopter is pointed at did not contain the vocabulary the tools require.
Nobody noticed for fifteen rounds, including every adopter who worked around it
by inventing their own fields.

## Round 16 — the uncoached one, and the vocabulary fix worked

The uncoached adopter was the first adoption run with no coaching: no field list, no
predictions to score, nothing from the author about `serves:` or `constrains:`.
It was the only instrument that could test the previous round's fix, because
the other three corpora had been told about those fields directly.

**The result is unambiguous. Six of six decisions carry `invariant:` *and*
`constrains:`, and no field was invented.** The consulting adopter's rate under the old
`CLAUDE.md` was 1 of 23, and three earlier corpora invented `supersedes`,
`intent:` and `owner` between them. The vocabulary table was the whole
difference.

It also used the record on real work unprompted: `bin/relevant` plus
`bin/constraints` surfaced the evidence, the decision, and the sentence saying
the docs were still wrong, and it then fixed four documents so that all eight
agree. That is the loop working as designed, which has not happened in a cold
session before.

### Four findings, and one of them is a root cause I had dodged twice

**`bin/adopt` was not idempotent, and `bin/prove` caught it on the very first
command put through it.** The root cause was one line: `graph-build` wrote
unconditionally, though `--check` had always compared modulo the timestamp.
Three symptoms had come from that line — `prove`'s idempotence assertion
becoming a coin flip, `bin/update` reporting churn it had not caused, and now
`adopt` silently rewriting the graph. **Twice I fixed a symptom of it.** It now
leaves an unchanged graph alone.

**An `invariant:` that was false the day it was written.** Their `D0014`
claimed every resume PDF is at most two pages; one is three. `bin/lint` passed
and `bin/stale` said *"Nothing contradicts the repository"* — because **nothing
validates an `invariant:` against the artifact it governs**, and in general
nothing can. They caught it by reading `constraints` output, which is the only
instrument there is, and amended through the sanctioned exit.

D0005 called `invariant:` a *machine-readable* claim. That was an overclaim and
it cost somebody a false decision on their first day. It is **surfaceable, not
checkable** — a tool can put it in front of you; nobody has verified it — and
both the decision and `bin/constraints` now say so. The one mechanisable case
is a countable invariant measured against a per-project fact, which is T0004:
speculative when written, now with a cost attached.

**`bin/utterances --repeated` ranked a teammate agent's idle JSON as the
operator's most-repeated statement** — seven times, labelled `[typed]`, the
cleanest possible provenance for something no person said. `INIT.md` tells a
migrator to mine that ranking for the operator's own words, so **the tool
`INIT` recommends was supplying the laundering `INIT` forbids.** Third distinct
instance of this class and the first to arrive through the tool rather than
around it. Machine payloads are filtered now, narrowly, because a false
positive drops one sentence and a false negative publishes another agent's
output as a founder's decision.

**"Embedded but gitignored" is a third mode the docs never named.** A public
repository whose owner did not want the brain published — entirely reasonable,
and nothing flagged that the premise of embedded, co-versioning, was absent.
`doctor` said sound because soundness is about the install. It warns now, with
`git check-ignore`, and points at `mode: sibling` as the arrangement that gets
history back.

### Two gaps in INIT, both about being alone

`INIT.md` said *"do not proceed without the fourth"* — the goal — with **no
exit for there being nobody to ask.** Unattended, the only options it offered
were to stop or to invent an answer, the one thing the file forbids. It now
says: write the goal from what the operator has already said, quote the
sentence, leave it `draft`. A draft goal terminates a lineage honestly and
`lint` counts drafts on every run, so it cannot be quietly forgotten.

And **the first session cannot finish a task.** Skills load at session start
(D0010), so the session that installs the brain has no `/spec-review`, which
owns `Testing → Complete`. Work done in the first session parks at `Testing`
however complete it is. Reported as a defect; it is a consequence of D0010, and
the fix is saying so in advance rather than letting somebody set `Complete` by
hand.

### What this round says about the project

Three of the four findings are defects in tooling. None is in the model. And
the one measurement that mattered — can an agent given only `CLAUDE.md` write
a decision that the tools can actually use — went from 1-in-23 to 6-of-6 by
adding a table of fields and what their absence costs. **The model was never
the thing that failed to travel. The vocabulary was missing from the file
everybody was pointed at.**

## Round 17 — the report was never a measurement

The uncoached adopter updated clean: every node, both feedback files and all `config.md`
values byte-identical, and all five of their previous findings confirmed
landed. One thing got worse, and it is the same thing that has got worse three
times.

**`bin/update`'s report was false for 4 of the 9 paths it claimed** — including
`updated skills/ (7 file(s))` over a directory that had not changed at all
between those two revisions. Their own words: *this is the exact regression
your README documents as already found and fixed, down to the same "7 files" in
`skills/`.*

Reproducing it found the inverse as well. A brain at their revision, updated to
HEAD, claimed **2 paths** and changed **18 files**. Both directions, one cause:

**The report listed what the copy loop intended, and three transforms run after
it that write files none of them mention** — the template re-stamp, the
citation repoint (a subprocess whose output is captured and discarded), and the
`config.md` merge. So it undercounted by everything those touched, and
overcounted whenever a copy was decided and then made no difference.

Three previous fixes went into the reporting layer — honest per-path counting,
then the `.upstream-manifest`, then discounting relocated nodes — and none held,
because **the report was never derived from what happened.** It is now: the
tool hashes the tree before and after itself and prints the difference. 18
reported, 18 actual; second run 0 and 0. It cannot drift from the truth, because
it is computed from it.

### The blind spot that hid it

Their sharpest observation, and it is about the instrument rather than the bug:

> `bin/prove` cannot catch this one. I ran it afterward and it passed,
> correctly, because by then the update was a no-op and it honestly reported
> doing nothing. Prove only tests from current state, so a false claim on the
> updating run is invisible to it unless you snapshot beforehand.

**The defect `prove` exists to catch has found the one shape `prove` cannot
see.** `prove` had been run on `bin/update` repeatedly across three releases
and passed every time, because every run was the second one. The fix is not in
`prove` — it cannot see a past it was not present for — it is in the tool, and
the general rule is now written into `prove` itself: *a tool that reports a
measurement does not need this one.*

### Two smaller things

**A hand-edited node leaves its folder index stale and nothing warns.** They
set a task's status directly; the initiative's index went on showing the old
one until an unrelated update happened to regenerate it. `lint` checks indexes
now — by content, not mtime. The mtime version flagged four indexes when one
node had changed and would not clear after a rebuild, because a generator that
rewrites only what differs leaves the rest older than everything around them. A
check with false positives it cannot clear is one people switch off. The
content version then needed a second correction: which column holds the status
has to be read from the header, because the decisions index puts type there and
a fixed offset reported every principle as a wrong status.

**`bin/utterances` count inflation is still open.** The machine-payload half is
fixed — the teammate-idle JSON that topped the ranking is gone and operator
utterances dropped 15 to 10 — but a line said once still shows ×4, because
replayed context is re-counted. `--repeated` remains unreliable as a frequency
signal and `INIT.md` should not be read as trusting it.

## Round 18 — the count was inflated by the longest message in the project

The last known-false thing in the toolchain, left open at the end of round 17
and closed by measuring it on this project's own transcript rather than
reasoning about it.

`bin/utterances --repeated` reported three statements as the operator's most
repeated — ×5, ×4, ×2. **Every one had been said exactly once**, which was
checkable because the session that mined them was the session that produced
them.

**The first diagnosis was wrong, and writing it down is the point.** I assumed
replayed context: a transcript re-sends earlier messages, so the same text
appears many times in one file. That is true, and deduping by `(session, text)`
is a correct fix for a real thing — but it changed none of the counts, because
the duplicates were not exact.

The actual cause was the similarity measure:

```python
overlap = len(wi & wj) / max(1, min(len(wi), len(wj)))   # containment
```

Dividing by the **smaller** set makes it containment, not similarity. Any short
message whose content words all appear in a long one scores 1.0 and is counted
as a repetition of it. So the inflation grew with the length of the longest
message in the project, which is why it showed up worst on this brain, where
the operator pastes multi-thousand-character adoption reports.

True Jaccard — divide by the union — collapses the whole thing. The tool now
reports **one** repeated statement in this session, ×2, and that one is real:
the same feedback round was pasted twice.

Both fixes are kept. The dedup is right even though it was not the bug, and
saying so is cheaper than pretending the first diagnosis was the finding.

**`INIT.md` can now be read as trusting this ranking**, which it could not
before. A migrator following its instruction to mine the operator's own words
was being handed, in order: another agent's JSON (fixed in round 16), and then
whichever of their own sentences happened to be a subset of their longest
paragraph.

## Round 19 — the evidence was in a chat log, not the record

Several rounds ago the type counts were measured and a recommendation made —
drop `spec`, mark Approach optional — and then nothing was written down. The
evidence lived in a conversation, which is the exact failure D0001 exists to
prevent, in the project that exists to prevent it. D0013 now holds it.

Re-measured across five brains rather than recalled, which changed one number
and the conclusion:

**Four of five have no plans. Three of five have no specs.** `decision`, `task`
and `evidence` are 219 of 235 nodes, 93%, and appear in every corpus. The one
brain using Approach uses it heavily — 30 plans — and is the only multi-person
product team in the set.

So Approach is not weak, it is **conditional on the work having a planning
phase**. The tools had already been corrected one at a time as each adopter hit
it, but **nothing said it out loud**: three adopters each worked out alone that
the layer they were skipping was optional, and one abandoned `bin/ancestry`
because every task read `⚠ UNTERMINATED`. The cost of saying it was one word in
a table.

### The part worth recording is the reversal

I had recommended deleting `spec` on a count of four nodes in 235. **That does
not survive contact with the cost.** Deleting it means a migration imposed on
two brains that did nothing wrong, removing or repointing `/to-spec`, and
editing D0006, which distinguishes code review from spec review — to save one
line in a seven-question ladder where `spec` is already question six.

**Rarity is not uselessness, and a count table makes the two easy to confuse.**
`goal` is 11 nodes across five brains and `principle` is 2, and both are
load-bearing: one goal anchors sixty tasks, and you are supposed to have few.
The honest reading of a small number is "ask what it costs to keep", not
"delete". The recommendation was made from the table alone and was wrong.

### What it does not fix

`/grill-change → /to-spec → /to-tasks → /implement` is still written as one
path, and for four of five corpora the middle step has never been taken. The
skills are unchanged, and D0013 says so rather than implying the decision did
work it did not do.

Writing it also broke a check immediately, correctly: D0013 declares
`constrains: bin/handoff`, so a fixture asserting that `bin/handoff` matched
nothing began asserting the absence of a message the tool was right not to
print. The fixture now asks about a path no invariant governs. A check whose
premise is "the record is empty here" ages the moment the record fills.

## Round 20 — the skills taught the path four of five corpora never take

D0013 named this and left it. Checking it first corrected my own claim: the
forward chain is **not broken**. `bin/ancestry` follows a markdown link in a
task's body, so a task cut from a conversation reaches its decision today.
Saying that plainly was worth more than the dramatic version.

The real gap is narrower and in three documents:

* **`CLAUDE.md` wrote the chain as four mandatory arrows** —
  `/grill-change → /to-spec → /to-tasks → /implement` — with nothing marking
  the middle step optional.
* **`to-tasks` step 1b existed only in the spec form.** Titled *"Read what the
  spec serves"*, running `ancestry <the spec id>`. With no spec there was **no
  instruction at all**, so the discipline that step exists for — read the
  intent before cutting tasks — silently did not happen for the majority case.
* **The task template offered `spec:` and never `serves:`**, so an agent
  without a spec fell back to a body link. That works, and it is the weaker
  form: `lint` treats a dangling typed edge as an error and a dangling body
  link as a warning. The README's own line is *a typed edge is enumerable; a
  body link is a hope* — and the skill was teaching the hope to four corpora
  out of five.

Step 1b now branches, with `bin/relevant` and `bin/constraints` as the
conversation route — the same intent-reading discipline by the path that
actually exists for these brains. The template offers both edges with an
instruction to delete the one that does not apply. Verified end to end: a task
written the way the skill now describes lints clean and terminates on its
decision.

The checks added here are text assertions about documents, which is a weak kind
of check. Recorded as weak rather than dressed up: they catch the edit being
reverted, not the guidance being wrong.

## Round 21 — the boundary was already half-crossed

T0011 asked whether artifacts outside the repository are in scope. Checking
before deciding made the question smaller and the answer better.

**Part of the boundary was already crossed, and working.** A node names an
external artifact with `resource:`; `bin/lint` errors when the target does not
exist and is silent when it does; `ancestry` and `graph-build` traverse it. Two
adopters use it today. Verified on a fixture with a real file outside the brain
and then a missing one, rather than read out of the code.

So the question was never *can the record point outside itself*. It was **what
the record is entitled to claim about what it points at** — and D0014 answers:
that the file is there, and nothing more.

### Why the tripwire lost

The rejected alternative was a drift check: path plus content hash, warning
when the file exists and differs, silent when absent so a teammate on another
machine is not spammed. Genuinely verifiable, and **it can fail**, which this
record rates highly.

It lost on maintenance. The hash is stamped by hand, so a deliberate edit to
the external file means re-stamping or a warning that fires forever — **the
permanently-red pattern already paid for twice here**, once with the `example/`
failures an adopter watched for two rounds and stopped reading, once with
`bin/unread` on a field a brain had kept deliberately. Paths under `~/` are also
machine-specific: the artifact is visible to one person and the node is
committed for everybody.

One failure, once, does not buy a standing obligation. The condition for
revisiting is written into the decision rather than left to memory: a brain
where external artifacts are the *subject* rather than an edge case — agent
configuration, skills, profiles — changes the arithmetic.

### What it does not fix, stated rather than implied

A correctly captured finding whose target lives outside the repo can still fail
to land. **D0014 makes the gap legible; it does not close it.**

That is the right trade here because of what the eight hours actually cost. The
finding was captured. The record was complete and correct. What was missing was
anybody having *said* the gap existed — so a complete record reasonably read as
a landed change. A sentence in `CLAUDE.md` would have cost nothing and saved
all of it, which is a cheaper fix than the check and addresses the failure that
actually happened.

## Round 22 — the goal that was never written, and the task nobody closed

Two pieces of record rot, both of the same kind: something already known, never
written where a tool or a reader would meet it.

**For twenty-one rounds the only goal on record was about adoption.** G0001
measures whether the thing installs; nothing measured whether it was worth
installing. Asked directly whether the brain is worth using, the honest answer
was that the question was *partly unanswerable* — **you cannot fail at a goal
you never stated.** That is D0001's own target, sitting in the goal layer of
the project built to prevent it.

G0002 exists now, and it is written with numbers that already exist rather than
aspirations: ceremony under 10% against the 15–25% measured before `bin/note`
and the ~5% after; a tool the operator reaches for unprompted, with the
explicit corollary that **a tool nobody reaches for is a cost, not a feature**
and the honest response to a persistent one is deletion; a mistake the record
demonstrably prevented and *named by somebody else* — three exist, all from an
append-only journal rather than the typed graph; and the record being consulted
when it is inconvenient, which is the one supersede discipline earned on a day
an adopter was wrong three times.

It also carries what would disprove it: an adopter who keeps the record
faithfully for a quarter and cannot name a mistake it prevented. Untested,
because every corpus here is younger than the question.

**And T0002 sat at `Ready for Development` on the claimable frontier while
three separate conversations decided against building it.** Any agent running
`bin/status` would have picked up work already abandoned. The brain has the
mechanism — `wontfix`, which `bin/status` honours — and the reasoning belongs
in the node rather than in a chat log: ownership is only worth modelling where
more than one person competes for work, and the only multi-person corpus never
asked for it in ten rounds while carrying the most tasks of anyone. What would
reopen it is written down too, and it is a complaint somebody makes, not a gap
to anticipate.

### A check that was wrong to write

The first version asserted that some goal in the brain measures value. It
failed, correctly: `bin/adopt` relocates goals into `example/`, so an installed
brain has none by design. But the deeper reason not to keep it is that
**whether an adopter's catalog contains a goal about value is not the
template's business** — holding their record to the shape of ours is the
host-dependence mistake this suite has now made seven times. Removed, with the
reasoning left in the source so the next person does not re-add it.

The check that stayed is about behaviour: a `wontfix` task is off the frontier.

## Round 23 — the one class of invariant a machine can check

T0004 was written from a portability gap: adding a count to `bin/stale` meant
editing Python, so a project wanting "tests" or "migrations" checked went
without. It sat for weeks as a reasonable-sounding improvement nobody had a
cost for.

**What justified building it was a different failure.** An adopter wrote an
`invariant:` claiming every resume PDF is at most two pages. One was three.
`bin/lint` passed and `bin/stale` reported *"Nothing contradicts the
repository"* — because `pages` was not a noun it could be taught. D0005 was
corrected into *surfaceable, not checkable* on the strength of that.

`brain/facts.md` is a markdown table of `| noun | command |`, and it makes the
narrow case checkable after all: **a countable invariant measured against a
number the project can produce on demand.** That is the only class a machine
can verify, and claiming more would be the overclaim D0005 was corrected for.
Reproduced the original failure to prove it: *"says 2 pages; there are 3"*,
exit 1.

Three things it is honest about rather than quiet about:

* **The declared commands execute when `bin/stale` runs.** Same trust model as
  a Makefile in a checked-out repository, said in the tool because it turns
  `bin/stale` from a reader into an executor. A file nobody wrote runs nothing.
* **A command that cannot run is reported, never counted as zero.** `widgets:
  exit 127` appears as a note; it does not silently make every "0 widgets"
  claim true.
* **A pipeline that silently fails can still yield a plausible number** —
  `cat missing | wc -l` is `0`. Nothing guards that and nothing can in general,
  because zero is often a legitimate count. Written down because the next
  person to be bitten should find it already known.

### The defect in the writing

The docstring promised that a command containing a pipe works. The parser split
on every `|` and truncated it at the first. **The claim was about to be false
in the tool that exists to catch false claims** — caught by reading my own
example back against my own code, which is the cheapest review available and
the one most often skipped. Split once, with a check.

The same round closed T0002 as `wontfix`, which leaves one task on the
frontier. The useful shape of this round: a task written on a plausible gap sat
untouched for weeks, and was built the week somebody produced a real failure
for it. That ordering is worth keeping.

## Round 24 — the frontier is empty

T0003 was the last task on the claimable frontier: group the graph legend by
the four categories, and put ids in node labels so a node seen in the picture
can be quoted without opening it.

Both done, with one `CATEGORY` map feeding the legend's grouping *and* the
`@intent` family of filters, so the picture and the search cannot drift about
what a category contains. That shared-source habit is the same one that fixed
`bin/update`'s report two rounds ago: **derive the second thing from the first
rather than writing it twice.**

**Verified by rendering it.** `file://` is not reachable from the browser tool,
so it was served over localhost and driven directly: `@intent` 16 hits,
`@approach` 1, `@action` 11, `@evidence` 3, and `@decision`, `@draft` and
`#brain` unchanged. A sample label came back `D0001 The record is only worth…`.
Reading the generator would have shown the code was written; it would not have
shown that the filters resolve or that the labels fit.

The checks that remain assert generated source, because the behaviour lives in
a browser the suite cannot drive — recorded as weak in the same terms as the
skill-path checks. **The pattern across three rounds now: when a change lands
somewhere a check cannot reach, verify it by hand once and say in the record
that the check is weaker than the verification was.**

### Where that leaves the work

Every task in `formalize` is closed. The frontier is empty for the first time
since the initiative opened, and what is left is not tasks:

* An adopter's brain updated after their delivery, to see whether a
  `constrains:` rate moves from 1-of-23 now that `CLAUDE.md` names the field.
* G0002's signals, none of which can be read until somebody has kept a record
  for a quarter.
* Whether the README still oversells the typed graph relative to what five
  corpora actually used — D0013 exists to cite, and nothing has cited it.

None of those is work this repository can do to itself, which is a different
state from having a backlog.

## Round 25 — a fourth class: documents that contradict each other

Rewriting the README in the operator's own voice surfaced something the rewrite
was not looking for. The README now says Approach is optional, because D0013
says so. `CLAUDE.md` says it. **`knowledge-catalog/howto.md` did not**, and had
not for the full day since the decision was written.

D0013's invariant has two halves: *no tool requires a brain to have an Approach
node, and **no document implies one is expected.*** The tool half was enforced
the day it was written. The document half was violated by one of the three
documents that carry the category table, and nothing compared them.

That is a **fourth defect class**, alongside the three this record already has
instruments for:

| class | instrument |
|---|---|
| a tool's output disagrees with the filesystem | `bin/prove` |
| a change infers the shape of a corpus it cannot see | D0011 |
| the record carries a field no tool reads | `bin/unread` |
| **two documents disagree, or a document contradicts a decision's invariant** | **nothing general** |

The narrow case is checkable and now is: any generic document carrying the
category table must mark the Approach row optional. Verified to fail by
reverting `howto.md`. The general case — *do these two documents agree* —
has no instrument and probably cannot have one, which is worth stating rather
than leaving as an implied gap.

Worth noticing where this came from. The inconsistency was invisible for a day
and surfaced the moment somebody rewrote one of the three documents from top to
bottom. **A full rewrite is a review of everything the document asserts**, and
it found a contradiction that twenty-four rounds of targeted edits had not.

### Eighth instance, same shape

Testing the new check from a scratch copy produced a second failure:
`solo2 · update records the revision even when the key was absent`. Not a
regression. The revision comes from `git rev-parse` in the source, and I had
removed `.git` from the copy, so there was nothing to record.

An adopter running `bin/selftest` from a brain whose source is not a git
repository would have hit exactly that, and read it as a defect in their
install. Skip when the precondition is absent, fail when the assertion is
false — eighth time that rule has been applied, and the first time it was
caught by a scratch fixture rather than by somebody else's report.
