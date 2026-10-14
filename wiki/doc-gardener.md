---
title: Doc Gardener Loop
category: pattern
tags: [drift, claude-md, agents-md, documentation, scheduled-agent, maintenance, knowledge]
sources: ["[[2026-08-27-ai-it-services]]", "[[2026-08-06-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A doc gardener is a recurring agent — nightly or weekly — whose only job is to check that `CLAUDE.md`/`AGENTS.md`, skills and project docs still match the code, and to prune or resync them: the automated answer to memory-file [[drift]].

## Where the name comes from

OpenAI's [*Harness engineering*](https://openai.com/index/harness-engineering) write-up — the team that "stopped opening the editor" and runs an internal [[agentic-corporation|dark factory]] — describes a recurring *doc gardening* task that keeps its markdown in sync with the code and consolidates it on a schedule ([[2026-08-27-ai-it-services]], [[2026-09-18-ai-agentic-how]]).

## Why you need one

Every other knowledge practice in this wiki *adds* text: [[self-improving-knowledge]] adds a rule after each frustration, [[agents-md-discipline]] adds the line that would have prevented the last escape. Nothing removes text, so memory files rot in two ways:

- **Drift** — module names, commands and architecture notes go stale as the code moves; the agent then follows a map of a city that no longer exists. Old specs that drift and derail agents are what one talk called [[vibe-heresy]] ([[2026-10-06-gray-factory-devoxx]]).
- **Bloat** — "update AGENTS.md on every change" grew one file to 2,000 lines; every line costs tokens on every turn and dilutes attention ([[context-window]], [[dumb-zone]]).

The gardener is the counterweight: it removes, deduplicates and resyncs. Rules are *maintained*, not accumulated — in effect, an `/init` that runs continuously ([[2026-08-31-ai-software-vendor]]).

## What it does

Variants seen across teams:

- **Resync to code** — an autonomous agent comes in at night and checks whether `CLAUDE.md` still matches what changed in the code that day ([[2026-09-07-ai-bank]]). One team's cron at 23:30 sends agents through **every folder** to update the nested AGENTS.md files ([[2026-09-16-ai-safety-critical]]).
- **Prune the obvious** — ask the weaker day-to-day model *"is this line needed, or obvious?"* and delete everything it calls obvious; Claude Code's `/doctor` now proposes similar trims, cutting what Claude can derive from the codebase ([[2026-09-04-ai-software-company]]).
- **Reconcile across levels** — dedupe rules between home, project and folder files and align the docs with the code ([[memory-layers]]).
- **Garden the skills too** — `/context` in a fresh session shows where the start-up tokens go; one session counted 49 skills ≈ 7k tokens before the user typed anything. Drop skills that never fire ([[agent-skill]]).
- **Propose, don't push** — a weekly agent that opens *shrink PRs* for a human to approve ([[2026-09-28-ai-gaming-tech]]); the [[agentic-review-pipeline|review]] gate applies to markdown as much as to code.
- **Fold the week into the wiki** — the same loop maintains an [[llm-wiki]]: a skill in crontab, run in the cloud rather than on a laptop, ingests **only the diff since the last commit** with sub-agents on a weaker model. (The initial full ingestion burned a week of top-model quota — run the incremental one.) ([[2026-09-02-ai-insurer]], [[2026-09-25-ai-bank-migration]])

Scheduling: weekly is the common cadence, nightly where the code moves fast. One cheap trick: spend leftover monthly quota at the end of the cycle having agents prune markdown the code no longer backs ([[2026-09-24-ai-bank]]).

## The manual version

Before automating it, do it by hand when you notice the symptom: tell the agent *"the module names listed are stale, update that section"* ([[2026-08-06-ai-bank]]), or ask it to rewrite only the "Project Overview" section after an architecture change. Fix drift in a **new** chat with the offending line attached, so the correction isn't contaminated by the session that tripped over it. If the same fix keeps recurring, that's the signal to schedule the gardener.

## Beyond markdown

The same idea scales up: LangChain's Deep Agents were framed in one session as an agent that cleans knowledge of contradictions and drift ([[2026-08-13-ai-insurer]]); memory systems such as Mnemosyne push it further, distilling facts into habits and principles ([[two-layer-memory]]). The gardener is also one of the standing scheduled agents of a [[gray-factory]] — next to the [[policeman-agent]] and the [[enterprise-architect-agent]] — that run on a clock rather than on request.

## See also
- [[drift]]
- [[claude-md]]
- [[agents-md]]
- [[agents-md-discipline]]
- [[self-improving-knowledge]]
- [[llm-wiki]]
- [[vibe-heresy]]
- [[ralph-loop]]
- [[policeman-agent]]
- [[gray-factory]]
