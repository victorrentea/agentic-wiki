---
title: Harness
category: concept
tags: [agent-harness, claude-code, tooling, knowledge, actions]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-11
updated: 2026-06-22
---


The harness is everything wrapped around the raw model: a human interface plus a set of tools that give the LLM **knowledge** (CLAUDE.md, skills, wiki, code graph) and **actions** (run builds, deploy, git, restart prod).

## Harness ≠ model

The **model** is the LLM itself (Opus, GPT, DeepSeek) — literally a file of floating-point weights that turns input tokens into output tokens. The **harness** is the program that turns that model into an agent — managing the [[context-window]], injecting the [[system-prompt]], exposing [[tool-calling]], and orchestrating turns. When a colleague says "we use Claude," ask whether they mean the model or the harness: they are separable and independently swappable.

<span style="color:red">Swapping one for the other is the whole point of [[claude-code-router]]: same harness, different model underneath. Cross-harness project memory is standardized as [[agents-md|AGENTS.md]] (symlink `AGENTS.md → CLAUDE.md` to serve both Claude Code and Codex from one file), but hooks, skills, and permissions remain harness-specific and don't port.</span>

Examples named in the field: **Claude Code**, **Cursor**, **Codex**, **OpenCode**, and **Glean** (an in-house "ChatGPT" you type *into* — you plug in codebases, Slack, and Jira, and it speaks [[model-context-protocol]]). Orchestration features are usually built around the model a harness ships with, so they don't always port.

## What lives in the harness

Everything that arrives in the model on turn 1 — see [[system-prompt]] for the full inventory: harness/system prompt, your message and its files, built-in plus MCP [[tool-calling]] schemas, [[claude-md]] memory, and the eager-loaded [[agent-skill]] front-matter. The harness is therefore where [[token-economy]], [[progressive-disclosure]], and [[tripwire]] enforcement all live.

## See also
- [[claude-code-router]]
- [[agents-md]]
- [[system-prompt]]
- [[model-context-protocol]]
- [[agent-skill]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
