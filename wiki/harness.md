---
title: Harness
category: concept
tags: [agent-harness, claude-code, tooling, knowledge, actions]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

The harness is everything wrapped around the raw model: a human interface plus a set of tools that give the LLM **knowledge** (CLAUDE.md, skills, wiki, code graph) and **actions** (run builds, deploy, git, restart prod).

## Harness ≠ model

The **model** is the LLM itself (Opus, GPT, DeepSeek). The **harness** is the program that turns that model into an agent — managing the [[context-window]], injecting the [[system-prompt]], exposing [[tool-calling]], and orchestrating turns. Swapping one for the other is the whole point of [[claude-code-router]]: same harness, different model underneath.

Examples named in the field: **Claude Code**, **Cursor**, **Codex**, **OpenCode**, and **Glean** (an in-house "ChatGPT" you type *into* — you plug in codebases, Slack, and Jira, and it speaks [[model-context-protocol]]). Orchestration features are usually built around the model a harness ships with, so they don't always port: running Claude Code on an OpenAI key works, but front-matter, hooks, and CC-specific workflow features may not survive the swap.

## What lives in the harness

Everything that arrives in the model on turn 1 — see [[system-prompt]] for the full inventory: harness/system prompt, your message and its files, built-in plus MCP [[tool-calling]] schemas, [[claude-md]] memory, and the eager-loaded [[agent-skill]] front-matter. The harness is therefore where [[token-economy]], [[progressive-disclosure]], and [[tripwire]] enforcement all live.

## See also
- [[claude-code-router]]
- [[system-prompt]]
- [[model-context-protocol]]
- [[agent-skill]]
- [[token-economy]]
- [[2026-06-11-ai-playtika]]
