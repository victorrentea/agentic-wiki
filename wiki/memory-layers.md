---
title: Memory Layers
category: concept
tags: [claude-md, agents-md, team, company, skills, knowledge-engineering]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-05-ai-consultancy]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-04-ai-software-company]]"]
created: 2026-06-23
updated: 2026-10-13
---

<span style="color:red">Memory layers are the nested scopes an agent's rules come from — user, project, team, and company — all loaded into context together, with the most specific read last.</span>

## The four layers

- **User** (`~/.claude/CLAUDE.md`) — global and *selfish*, applied across all your projects (your personal taste and habits).
- **Project** (`AGENTS.md` at repo root) — the team's ground rules for *that* repo. See [[agents-md-discipline]].
- **Team** — a **separate Git repo** of shared skills and rules for a *family* of repos (e.g. 4 devs across 7 repos). This is the [[cross-repo-knowledge]] central-team-repo pattern — knowledge lives on the team, not on one laptop.
- **Company** — a company-wide base layer (often a Git repo + wiki, sometimes with its own IDE extension and CLI plugin), under which the team and project layers stack.

## What the harness actually does

<span style="color:red">Layering is **not override**. Claude Code concatenates every discovered CLAUDE.md, from the filesystem root down to the working directory, so the more specific file is read *last*; nothing replaces anything, and when two instructions contradict each other *"Claude may pick one arbitrarily"* ([docs](https://code.claude.com/docs/en/memory#how-claude-md-files-load); [[2026-08-27-ai-it-services]]). Files at or above the working directory load at launch; CLAUDE.md files in subdirectories load lazily, the first time the agent touches a file there. A conflict between layers is therefore a [[hallucination]] risk, not a resolved precedence — fix it, don't rely on order.</span>

<span style="color:red">Claude Code's own four scopes differ from this page's taxonomy: **managed policy, user, project, local** (`CLAUDE.local.md`, read after `CLAUDE.md` in the same folder) ([docs](https://code.claude.com/docs/en/memory#choose-where-to-put-claude-md-files); [[2026-09-04-ai-software-company]]). The team and company layers below have no CLAUDE.md scope of their own — they arrive as a cloned repo, [[plugin-marketplace|plugins/marketplaces]] or managed policy. Since v2.1.277 (Sep 2026) Claude Code also reads `AGENTS.md` natively, but by default only when no CLAUDE.md exists in or above the working directory; with both, put `@AGENTS.md` in CLAUDE.md ([docs](https://code.claude.com/docs/en/memory#agents-md); see [[agents-md]]).</span>

<span style="color:red">A five-layer view of what is in context at any moment ([[2026-05-11-ai-software-vendor]]): user memory (`~/.claude/CLAUDE.md`) → project CLAUDE.md (committed) → [[agent-skill|skills]] (auto or explicit) → the conversation itself (ephemeral) → the cross-repo central layer (pulled at `SessionStart`).</span>

## Why layer instead of merge

Stacking lets each scope stay small and stay out of the [[dumb-zone]]: the company base carries universal conventions, the team repo carries the family's shared skills, and the project file carries only what's special about this repo. Conflicting rules across layers just confuse the agent — so the layering must be **ordered**, and [[skills-governance]] (one skill-set per team, not company-wide and not per-dev) keeps it from degenerating.

<span style="color:red">In practice the **product layer** is usually the missing one ([[2026-04-27-ai-healthcare-software]]): the corporate-wide layer tends to be generic — how to use a Postgres MCP, a clean-code skill, often a barely used `awesome-ai-coding` repo — and *not* the place for project rules; the repo layer has AGENTS.md, nested folder rules and repo-private skills; but the rules shared by a core modulith and its satellite services (same architecture, style and stack) belong to a **product-specific** layer in between. That central repo carries skills, prompts, [[custom-agent|custom agents]], MCP server configs, other services' OpenAPI specs and C2 diagrams (C3 usually not; DB schemas sometimes) — see also [[enterprise-architect-agent]].</span>

The cross-tool filename for the project layer is [[agents-md|AGENTS.md]]; loading deeper context only when relevant is [[progressive-disclosure]]; the team layer's compounding loop (PR-to-central, reviewed each sprint) is described in [[cross-repo-knowledge]].

## Two flows: top-down and bottom-up

<span style="color:red">Layers only work if knowledge moves between them ([[2026-04-27-ai-healthcare-software]], [[2026-04-16-ai-safety-critical]]):</span>
- <span style="color:red">**Top-down update** — a scheduled morning `git pull` of the central repo plus a **session-start hook** that refreshes it before each conversation (Claude Code has `SessionStart`, see [[agent-lifecycle-hooks]]; for a CLI without one, wrap the binary in a script).</span>
- <span style="color:red">**Bottom-up escalation** — periodically (weekly to quarterly) **knowledge engineers**, an emerging role, scan the repos for rules and skills that have converged and promote them a layer up. The mechanics are in [[cross-repo-knowledge]] and [[self-improving-knowledge]].</span>

## The two-layer split for personal style

Within the user scope, the durable split is: **home `memory.md`** (personal communication style and challenge preferences — who you are, "always challenge me when I'm vague or contradictory," be concise, don't sycophant, match my language) vs **project `CLAUDE.md`** (domain rules and NFRs). Define both explicitly so the agent doesn't need re-prompting style every session. The `memory.md` at user scope is *selfish* (your personal taste); the project `CLAUDE.md` is the team's ground rules for that repo.

See [[two-layer-memory]] for the detailed breakdown.

<span style="color:red">**The user-vs-project trap.** Say *"remember this for the future"* and the agent may save it to **your user memory** instead of the project's CLAUDE.md/AGENTS.md — which is what goes to git and reaches your colleagues. Check where it wrote ([[2026-06-05-ai-consultancy]], [[2026-05-27-ai-open-workshop]]). Claude Code's auto memory lives in `~/.claude/projects/<project>/memory/` (one directory per git repo, shared by all its worktrees) and only the first 200 lines or 25 KB of its `MEMORY.md` index load at session start ([docs](https://code.claude.com/docs/en/memory#auto-memory)). User memory accumulates stale preferences too, and needs the same retrospect-and-prune treatment as the project file.</span>

## See also
- [[agents-md-discipline]]
- [[agents-md]]
- [[claude-md]]
- [[cross-repo-knowledge]]
- [[skills-governance]]
- [[progressive-disclosure]]
- [[two-layer-memory]]
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[enterprise-architect-agent]]</span>
- <span style="color:red">[[self-improving-knowledge]]</span>
- <span style="color:red">[[hallucination]]</span>
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-04-16-ai-safety-critical]]</span>
- <span style="color:red">[[2026-05-11-ai-software-vendor]]</span>
- <span style="color:red">[[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">[[2026-05-27-ai-open-workshop]]</span>
- <span style="color:red">[[2026-08-27-ai-it-services]]</span>
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
