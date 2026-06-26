---
title: Cross-Repo Knowledge (Dark Horses)
category: pattern
tags: [claude-md, skill, team, monorepo, knowledge-engineering, symlink]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-11
updated: 2026-06-26
---

Cross-repo knowledge is the pattern of keeping a team's shared agent configuration — [[claude-md]] rules plus [[agent-skill|skills]] — in one central git repo that every project repo references, so knowledge lives on the team rather than on one laptop.

## The "Dark Horses" central repo

A studio may have 20–50 repos, only a handful of them yours. Putting agent knowledge in each repo's local `CLAUDE.md` fragments it and lets it drift independently. The pattern:

- A **team git repo** (e.g. `dark-horses-ai-central`) holds the shared `CLAUDE.md` and skills.
- Each cloned project **symlinks** to it (a real setup wired ≈100 symlinks), or each repo's `CLAUDE.md` does `@include $DARK_HORSES_CENTRAL/...` against an env-var path every dev sets.
- Add a central **OpenAPI registry** repo so your agent can see a colleague's service contract, and **skill-extensions** so teams tailor the standard workflow.

## Microservices: the parent-folder session trick

<span style="color:red">When one team owns ≈10 repos, opening a Claude Code session inside a single repo makes the agent blind to the others. Fix: clone all repos under one root directory and open the session *in that parent folder* — each repo gets its own small, proportional `CLAUDE.md`, and the root directory becomes an **orchestrator CLAUDE.md** that knows the full landscape. For production robustness (VPS, CI, Docker, Jenkins), this "clone them all" approach breaks because the repos may not be present. The durable solution is the **`green-team-ai-central`** git repo: it holds shared skills, MCP configs, and agent personas, with the root CLAUDE.md a **symlink** into it ("it's a symlink — it IS versioned"). Every service repo symlinks to the central one; teams add skill-extensions for service-specific workflows. Someone argued agents are an incentive to merge microservices back into a monorepo — the symlink pattern is the alternative.</span>

## Knowledge engineering

The loop that makes it compound: when an agent misbehaves it opens a **PR to central**, reviewed each sprint. That review *is* knowledge engineering — "training the minions" — and it is exactly how a [[tripwire]] or a corrected [[claude-md]] rule propagates from one engineer's frustration to the whole team. The `@include` mechanism is plain [[progressive-disclosure]]; the OpenAPI registry feeds the [[field-reality-diagram]] and drift-check pipelines. A more automated version of this loop is [[self-improving-knowledge]] — a weekly agent that mines your own JSONL conversation logs and files PRs to the central repo automatically.

## See also
- [[claude-md]]
- [[agent-skill]]
- [[self-improving-knowledge]]
- [[progressive-disclosure]]
- [[tripwire]]
- [[drift]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-26-ai-agentic-how]]
