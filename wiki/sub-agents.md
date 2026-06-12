---
title: Sub-Agents
category: pattern
tags: [orchestrator, multi-agent, context-management, research, delegation]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


Sub-agents ("babies") are fresh-context worker agents an orchestrator spawns to research or review in parallel, each reporting back a few compact paragraphs — so the orchestrator escapes the [[dumb-zone]] without reading hundreds of files itself.

## Orchestrator + minions

The pattern's origin is Anthropic's [multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system): an orchestrator delegates to parallel workers, each of which burns *its own* context and returns only a distilled answer. It is one of the three escapes from the dumb zone (with [[handover]] and lossy compaction), and it shines when you would otherwise open hundreds of files — e.g. one baby per repo or module.

**The cost:** each baby starts from **context zero** and cannot see files the orchestrator already read, so Opus sometimes *refuses* to spawn one (it would have to re-read the POM). Use sub-agents when the re-read cost is worth the parallelism, not reflexively.

## Two concrete uses

- **Read-only research** for legacy code: the go-to [research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) is strictly *"do not suggest, do not propose, do not critique — just read, and return where to look next."* A summarization baby can also distill a human-written Confluence/Jira page (found via [[rag]]) before it floods context.
- **[[multi-model-review]]** — a "quorum of mothers-in-law", one sub-agent per review concern (clean code, performance, security, UX), split across model families to counter attention dilution.

## See also
- [[dumb-zone]]
- [[handover]]
- [[multi-model-review]]
- [[rag]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
