---
title: Cross-Repo Knowledge (Dark Horses)
category: pattern
tags: [claude-md, skill, team, monorepo, knowledge-engineering, symlink]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Cross-repo knowledge is the pattern of keeping a team's shared agent configuration — [[claude-md]] rules plus [[agent-skill|skills]] — in one central git repo that every project repo references, so knowledge lives on the team rather than on one laptop.

## The "Dark Horses" central repo

A studio may have 20–50 repos, only a handful of them yours. Putting agent knowledge in each repo's local `CLAUDE.md` fragments it and lets it drift independently. The pattern:

- A **team git repo** (e.g. `dark-horses-ai-central`) holds the shared `CLAUDE.md` and skills.
- Each cloned project **symlinks** to it (a real setup wired ≈100 symlinks), or each repo's `CLAUDE.md` does `@include $DARK_HORSES_CENTRAL/...` against an env-var path every dev sets.
- Add a central **OpenAPI registry** repo so your agent can see a colleague's service contract, and **skill-extensions** so teams tailor the standard workflow.

## Knowledge engineering

The loop that makes it compound: when an agent misbehaves it opens a **PR to central**, reviewed each sprint. That review *is* knowledge engineering — "training the minions" — and it is exactly how a [[tripwire]] or a corrected [[claude-md]] rule propagates from one engineer's frustration to the whole team. The `@include` mechanism is plain [[progressive-disclosure]]; the OpenAPI registry feeds the [[field-reality-diagram]] and drift-check pipelines.

## See also
- [[claude-md]]
- [[agent-skill]]
- [[progressive-disclosure]]
- [[tripwire]]
- [[drift]]
- [[2026-06-11-ai-playtika]]
