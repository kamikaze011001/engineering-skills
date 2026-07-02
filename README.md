# engineering-skills

A Claude Code plugin bundling skills for higher-quality engineering work. Installing
it adds all of the skills below at once.

## Install

```
/plugin marketplace add kamikaze011001/compounding-lessons-plugin
/plugin install engineering-skills@kamikaze-skills
```

## Skills in this plugin

| Skill | What it does |
|-------|--------------|
| `compounding-lessons` | Capture reusable, non-obvious code-review findings as durable searchable lessons so the same mistake is prevented, not re-reviewed. |
| `software-design-philosophy` | Design guidance based on John Ousterhout's *A Philosophy of Software Design* — complexity, module decomposition, API design, naming, comments, error handling. |

---

## `compounding-lessons`

When a review surfaces a **reusable, non-obvious** mistake, this skill captures it as a
durable, searchable lesson in `docs/superpowers/lessons/` so the same class of mistake is
*prevented* next time instead of re-reviewed. Cloned in spirit from compound
engineering's `/ce-compound`.

### Setup — required for the loop to work

This skill only *writes* lessons. It compounds only when future planning **reads**
them. Add these two lines to your global `~/.claude/CLAUDE.md`:

```markdown
Before brainstorming or planning work in a project, check docs/superpowers/lessons/
for relevant past lessons (grep by topic/tags) and apply their rules.

After receiving and addressing code review, use the compounding-lessons skill to
capture reusable findings there.
```

Without the first line the corpus is a write-only log, not a loop.

### Recommended companion

This skill references [`superpowers`](https://github.com/obra/superpowers) as its
natural trigger point (`superpowers:receiving-code-review` → this skill → finish
branch). superpowers is **recommended but not required** — the skill works standalone
whenever you finish addressing review findings.

```
/plugin marketplace add obra/superpowers
/plugin install superpowers
```

### What it does

1. **Extract** — for each qualifying finding, write Symptom / Root cause / Rule.
2. **Dedupe** — grep the lessons corpus; update an existing lesson (bump `times_seen`)
   rather than creating a duplicate.
3. **Persist** — one file per lesson at `docs/superpowers/lessons/<slug>.md` with
   tags/`problem_type`/`times_seen` frontmatter.

Capture a finding only if it is **both** reusable (likely to recur) and non-obvious
(a future agent would repeat it). Skip trivial nits.

---

## `software-design-philosophy`

A design-philosophy guide grounded in John Ousterhout's *A Philosophy of Software
Design*. Use it during code reviews, architecture discussions, API design, module
decomposition, refactoring, complexity analysis, and naming/commenting decisions.

Vendored from [`luoling8192/software-design-philosophy-skill`](https://github.com/luoling8192/software-design-philosophy-skill)
(MIT). Original license retained at `skills/software-design-philosophy/LICENSE`.

## License

- `compounding-lessons` — MIT.
- `software-design-philosophy` — MIT, Copyright (c) 2026 luoling8192 (see
  `skills/software-design-philosophy/LICENSE`).
