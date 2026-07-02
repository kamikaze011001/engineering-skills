---
name: compounding-lessons
description: Use when you have just received and addressed code-review findings and are wrapping up or finishing a branch, especially when a reviewer says a problem "came up before"/"third time", or you notice the same class of bug recurring. Use when a review surfaced a reusable, non-obvious mistake worth preventing next time rather than re-reviewing.
---

# Compounding Lessons

## Overview

A code review is the moment a mistake is best understood. Capture the *reusable* findings as durable, searchable lessons so the same class of mistake is prevented next time instead of re-reviewed. Each review makes the next one easier. (Learning capture, cloned from compound engineering's `/ce-compound`.)

**Core principle:** A lesson that lives only in a PR comment or a one-off issue is forgotten by its next occurrence. A lesson written to the lessons corpus compounds.

## When to use

After you receive and address code-review findings (e.g. via `superpowers:receiving-code-review` if the superpowers plugin is installed), before finishing the branch. Capture a finding ONLY if it is BOTH:

- **Reusable** — a pattern likely to recur (e.g. "N+1 query inside a loop"), not a one-off typo.
- **Non-obvious** — a future agent would plausibly repeat it.

A reviewer noting "this came up before" / "third time" is the strongest capture signal. Skip trivial nits, one-time mistakes, and facts already in the code or git history.

## The recipe

Produce these three steps, in order. This is a contract — the output is a lesson file in the fixed location below, nothing else.

**1. Extract.** For each qualifying finding, write three parts:
- **Symptom** — what the review flagged
- **Root cause** — why it happened
- **Rule** — one imperative, checkable sentence that prevents it next time

**2. Dedupe — search before you write.** Grep `docs/superpowers/lessons/` for the tags/topic. If a matching lesson already exists, **UPDATE** it (sharpen the Rule, increment `times_seen`) — do not create a second file for the same pattern.

**3. Persist.** One file per lesson at `docs/superpowers/lessons/<slug>.md` with this frontmatter:

```yaml
---
tags: [n-plus-one, database, performance]
problem_type: bug-pattern   # bug-pattern | design-smell | missing-test | convention
date: 2026-06-24
times_seen: 1
---
```

`times_seen` = how many times this pattern has been observed. Seed it from any known count (a reviewer saying "third time" → `times_seen: 3`), otherwise `1`. Increment on each dedupe update.

Body = the **Symptom** / **Root cause** / **Rule** sections from step 1.

## Reuse hook

Lessons compound only if future planning reads them. Your global `CLAUDE.md` must tell agents to check `docs/superpowers/lessons/` before brainstorming or planning. Without that line, this skill is just a log, not a loop. See this plugin's README for the exact lines to add.

## Quick reference

| Step | Action | Output |
|------|--------|--------|
| Extract | Symptom / Root cause / Rule | structured finding |
| Dedupe | grep lessons dir → update-or-create | no duplicates, `times_seen` bumped |
| Persist | write `<slug>.md` + frontmatter | one searchable lesson file |

## Common mistakes

- **Tracking instead of teaching.** Filing a "go fix the other instances" chore or GitHub issue is backward-looking cleanup. Also capture the forward-looking **Rule** so the pattern isn't *written* again.
- **Ad-hoc location.** Putting the lesson in `docs/anti-patterns/`, a spec file, or a PR comment. Use the one fixed path `docs/superpowers/lessons/` or the corpus isn't searchable and won't be found.
- **Skipping dedupe.** Creating a 4th file for a pattern that already has one. Search first; update and bump `times_seen`.
- **Capturing everything.** Logging trivial nits drowns the signal. Reusable AND non-obvious only.

## Example

Review flagged an N+1 query inside `exportOrders()`; reviewer noted it's the third time. After grepping `docs/superpowers/lessons/` and finding no existing match:

`docs/superpowers/lessons/n-plus-one-in-loops.md`
```markdown
---
tags: [n-plus-one, database, performance, loops]
problem_type: bug-pattern
date: 2026-06-24
times_seen: 1
---
# N+1 query inside a loop

**Symptom:** A function calls a per-row DB fetch (e.g. `db.getCustomer(id)`) inside a `for` loop over a collection.

**Root cause:** Per-item lookups feel local and correct; the aggregate query count is invisible at the call site.

**Rule:** Before looping over a collection, batch-fetch all related records in one query and index them in a map; never issue a DB call per iteration.
```

Next time a reviewer or planner greps for "n-plus-one" or "loop", this surfaces — and `times_seen` climbs instead of a new doc being born.
