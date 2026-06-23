---
title: Sub-Agents
category: pattern
tags: [orchestrator, multi-agent, context-management, research, delegation, router]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-11
updated: 2026-06-22
---


Sub-agents ("babies") are fresh-context worker agents an orchestrator spawns to research or review in parallel, each reporting back a few compact paragraphs — so the orchestrator escapes the [[dumb-zone]] without reading hundreds of files itself.

## Orchestrator + minions

The pattern's origin is Anthropic's [multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system): an orchestrator delegates to parallel workers, each of which burns *its own* context and returns only a distilled answer. It is one of the three escapes from the dumb zone (with [[handover]] and lossy compaction), and it shines when you would otherwise open hundreds of files — e.g. one baby per repo or module.

**The cost:** each baby starts from **context zero** and cannot see files the orchestrator already read, so Opus sometimes *refuses* to spawn one (it would have to re-read the POM). Use sub-agents when the re-read cost is worth the parallelism, not reflexively.

## Two concrete uses

- **Read-only research** for legacy code: the go-to [research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) is strictly *"do not suggest, do not propose, do not critique — just read, and return where to look next."* A summarization baby can also distill a human-written Confluence/Jira page (found via [[rag]]) before it floods context.
- **[[multi-model-review]]** — a "quorum of mothers-in-law", one sub-agent per review concern (clean code, performance, security, UX), split across model families to counter attention dilution.

## The router pattern

A pure-router main agent ("the perfect boss — just routing, doing nothing") dispatches specialized sub-agents instead of handling all domains itself. Example: a Slack bot that answers questions by calling 30 internal data APIs. Rather than making the main agent aware of all 30 APIs (context bloat), the main agent receives the query, selects the appropriate specialist sub-agent (sales data, bets, payments), and that sub-agent loads only the relevant API schema. Prefer the provider's OpenAPI/Swagger spec over hand-taught call instructions — hand-written ones drift from what is actually deployed at the next release. Rate-limit outbound calls from sub-agents (parallel spawning can machine-gun an API).

## See also
- [[dumb-zone]]
- [[handover]]
- [[multi-model-review]]
- [[rag]]
- [[token-economy]]
- [[firefighter-agent]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
