# compounding-lessons

A Claude Code skill that turns code review into a compounding asset. When a review
surfaces a **reusable, non-obvious** mistake, this skill captures it as a durable,
searchable lesson in `docs/superpowers/lessons/` so the same class of mistake is
*prevented* next time instead of re-reviewed.

Cloned in spirit from compound engineering's `/ce-compound`.

## Install

```
/plugin marketplace add kamikaze011001/compounding-lessons-plugin
/plugin install compounding-lessons@sonanh-skills
```

The skill then appears as `compounding-lessons` in the skill list on any machine or project.

## Setup — required for the loop to work

This skill only *writes* lessons. It compounds only when future planning **reads**
them. Add these two lines to your global `~/.claude/CLAUDE.md`:

```markdown
Before brainstorming or planning work in a project, check docs/superpowers/lessons/
for relevant past lessons (grep by topic/tags) and apply their rules.

After receiving and addressing code review, use the compounding-lessons skill to
capture reusable findings there.
```

Without the first line the corpus is a write-only log, not a loop.

## Recommended companion

This skill references [`superpowers`](https://github.com/obra/superpowers) as its
natural trigger point (`superpowers:receiving-code-review` → this skill → finish
branch). superpowers is **recommended but not required** — the skill works standalone
whenever you finish addressing review findings.

```
/plugin marketplace add obra/superpowers
/plugin install superpowers
```

## What it does

1. **Extract** — for each qualifying finding, write Symptom / Root cause / Rule.
2. **Dedupe** — grep the lessons corpus; update an existing lesson (bump `times_seen`)
   rather than creating a duplicate.
3. **Persist** — one file per lesson at `docs/superpowers/lessons/<slug>.md` with
   tags/`problem_type`/`times_seen` frontmatter.

Capture a finding only if it is **both** reusable (likely to recur) and non-obvious
(a future agent would repeat it). Skip trivial nits.

## License

MIT
