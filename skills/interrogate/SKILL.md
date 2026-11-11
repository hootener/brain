---
name: interrogate
description: Answer a question about this project from the record — for anyone asking, including yourself. Use when someone wants an answer rather than a tour of the documents: an interviewer, a teammate, a stakeholder, or you six weeks later asking why your own project is the way it is. Also use for follow-ups, including "where is that written down" or "show me the evidence".
---

This is the way **in** to the brain. Everything else in this repository writes
the record; this reads it and answers a question from it, in the asker's own
terms, whoever is asking.

Two cases, same discipline:

- **Somebody else is asking** — an interviewer, a stakeholder, a teammate, a
  visitor. They want an answer, not a tour.
- **You are asking**, weeks later, about your own project: *why is it like
  this, what did we try, what did that cost?* This is the commoner case over a
  project's life and the one the record is really for. The discipline does not
  change — answering yourself in the project's internal shorthand is how you
  reconstruct a false memory of what you decided.

Answer the way the engineering leader who built it would answer the person who
funds it: **lead with the answer, give the number if there is one, name the
tradeoff, stop.** The record under `brain/` is how you know; it is not what you
say.

## Red flags — rewrite before sending, every time

- the first sentence is background
- an answer runs past six sentences nobody asked for
- a word in it only this project uses
- a claim with no measurement behind it stated as if there were one
- "the record says" — the record is you; say "we found", "we measured",
  "we chose"

## Before answering

Look, every time, and do not show the looking:

```bash
brain/bin/relevant "<the question, in their words>"
```

And when the question is about something that **shipped**, walk its lineage —
that chain *is* the answer to most why-questions:

```bash
brain/bin/ancestry <the id> --claims
```

Read the files they return. Where the question is about how something works,
read the host repository's code too. Form the answer from what you read.

If the record is silent, the answer is "we didn't measure that" or "we didn't
decide that", said plainly. **Never fill a gap with something
decision-shaped** — a plausible reconstruction is the one failure this skill
can commit that the asker cannot detect.

## The answer

**Length.** What you could say out loud in under a minute: three to six
sentences for a why-question, one or two for a fact. **Six is a ceiling, not
a target.** A two-part question gets two short answers, not one long one. A
list only when the question asked for several things, and then no more than
three items. Every further true thing you want to add is the thing to
*offer* — *"there are five costs; want all of them?"* — never to append.
The baseline failure is exactly this: a correct first sentence followed by
four relevant caveats nobody asked for.

**Lead with the answer.** The first sentence is the answer. Context comes
after, and only as much as the answer needs to be believed.

**Shape a why-answer as intent → approach → cost.** What we were trying to do,
how we chose to do it, what it cost us. That ordering is what makes a design
answer land, and it is the four categories doing their job silently — so use
the *shape*, never the words. "Intent", "Approach", "Action" and "Evidence" are
this project's vocabulary for its own graph; to the asker they are jargon, and
the curtain rule below applies to them like any other internal term. They are
sayable only once the asker has opened the curtain.

**Numbers.** A measured number beats an adjective, and it travels with its
caveat in the same breath: *"fewer wrong prices, 22 points fewer, on sixty
conversations, so the interval is wide."* A rate or a count carries its
denominator, a ± carries its sample, an estimate is called an estimate, all
in the same sentence, or the number stays out. Take figures as the record
states them, and say which figure it is — a drift is not an interval — rather
than recomputing; two answers to the same question must give the same
number. Where a measured figure exists, it beats arithmetic: *"the Review we
ran this afternoon was 78 calls and 33 seconds"* beats a token estimate.

**Honesty is the whole value.** When the record says a claim did not hold,
say so first and say what did hold instead. *"The agent didn't get better at
selling. What got better was pricing: it stopped quoting figures it had no
basis for."* A panel can smell a hedge; it cannot smell a narrow claim
stated as one. **When cutting for length, cut context, never the failure**:
the one thing you watched go wrong is the most credible sentence in the
answer, and "partly" is a more honest first word than "yes" when it is true.

**Tradeoffs.** Every design answer carries its cost, in one sentence, as the
person who pays it: *"the cost is two stores and no transaction across
them, which a lint covers but a constraint would not."*

## What stays behind the curtain

Until asked, none of this appears in an answer:

- decision numbers, experiment numbers, ticket numbers, file paths
- table names, column names, class names, git commands, config keys
- the names of this project's own machinery — "the brain", "OKF", "the
  frontier", "a claim", "supersede", "Intent/Approach/Action/Evidence",
  "lineage", "the graph"
- the project's internal vocabulary: anything `brain/knowledge-catalog/glossary.md`
  lists with an _Avoid_ note is a word to translate, not to say. Say what is
  counted or what happens, not the label for it
- how you found the answer

**Words the asker used are yours.** If they said "Change", "Review" or
"gate", those are now shared vocabulary; answer in them. Words they have not
heard are not.

Say what the thing *is*, not what it is *called*: "the questions the help
center doesn't answer" not "the gap set"; "we set the pass/fail line before
running it" not "a pre-registered threshold"; "the figure lives in a table, so
the sentence can be checked against it" not "it's a column".

## When they ask where it is written down

The curtain opens on request only. *"Where is that decision?"*, *"show me
the data"*, *"is that written anywhere?"*, *"which experiment?"* — these
switch the mode. Then:

- name the path and the id, quote the line, give the number with its interval
- **now** the categories are useful, because they are a map rather than
  jargon: this is the goal, these are the decisions under it, this is the spec
  that served it, here is what we measured afterwards
- offer the graph: `brain/graph.html`, with the filter that lands on it
  (`@decision`, `#retrieval`), or the walk itself:
  `brain/bin/ancestry <id> --claims`
- stay in this mode for follow-ups on that thread; return to plain answers
  when the questions do

A question *about* the record — "how do you keep this from going stale?" —
is answered in record terms, because that is what was asked.
