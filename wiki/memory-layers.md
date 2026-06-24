---
title: Memory Layers
category: concept
tags: [claude-md, agents-md, team, company, skills, knowledge-engineering]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-24
---


Memory layers are the four nested scopes an agent's rules come from — user, project, team, and company — loaded by the harness in order so the most specific context wins.

## The four layers

- **User** (`~/.claude/CLAUDE.md`) — global and *selfish*, applied across all your projects (your personal taste and habits).
- **Project** (`AGENTS.md` at repo root) — the team's ground rules for *that* repo. See [[agents-md-discipline]].
- **Team** — a **separate Git repo** of shared skills and rules for a *family* of repos (e.g. 4 devs across 7 repos). This is the [[cross-repo-knowledge]] "Dark Horses" pattern — knowledge lives on the team, not on one laptop.
- **Company** — a company-wide base layer (often a Git repo + wiki, sometimes with its own IDE extension and CLI plugin), under which the team and project layers stack.

## Why layer instead of merge

Stacking lets each scope stay small and stay out of the [[dumb-zone]]: the company base carries universal conventions, the team repo carries the family's shared skills, and the project file carries only what's special about this repo. Conflicting rules across layers just confuse the agent — so the layering must be **ordered**, and [[skills-governance]] (one skill-set per team, not company-wide and not per-dev) keeps it from degenerating.

The cross-tool filename for the project layer is [[agents-md|AGENTS.md]]; loading deeper context only when relevant is [[progressive-disclosure]]; the team layer's compounding loop (PR-to-central, reviewed each sprint) is described in [[cross-repo-knowledge]].

## See also
- [[agents-md-discipline]]
- [[agents-md]]
- [[claude-md]]
- [[cross-repo-knowledge]]
- [[skills-governance]]
- [[progressive-disclosure]]
- [[2026-06-23-ai-garmin]]
