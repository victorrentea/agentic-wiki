---
title: Harness
category: concept
tags: [agent-harness, claude-code, tooling, knowledge, actions, cli]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]", "[[2026-06-23-ai-garmin]]", "[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-11
updated: 2026-06-26
---

The harness is everything wrapped around the raw model: a human interface plus a set of tools that give the LLM **knowledge** (CLAUDE.md, skills, wiki, code graph) and **actions** (run builds, deploy, git, restart prod).

## Harness ≠ model

The **model** is the LLM itself (Opus, GPT, DeepSeek) — literally a file of floating-point weights that turns input tokens into output tokens. The **harness** is the program that turns that model into an agent — managing the [[context-window]], injecting the [[system-prompt]], exposing [[tool-calling]], and orchestrating turns. When a colleague says "we use Claude," ask whether they mean the model or the harness: they are separable and independently swappable.

Swapping one for the other is the whole point of [[claude-code-router]]: same harness, different model underneath. Cross-harness project memory is standardized as [[agents-md|AGENTS.md]] (symlink `AGENTS.md → CLAUDE.md` to serve both Claude Code and Codex from one file), but hooks, skills, and permissions remain harness-specific and don't port.

Examples named in the field: **Claude Code**, **Cursor**, **Codex**, **OpenCode**, and **Glean** (an in-house "ChatGPT" you type *into* — you plug in codebases, Slack, and Jira, and it speaks [[model-context-protocol]]). Orchestration features are usually built around the model a harness ships with, so they don't always port.

**The harness matters as much as the model.** The same model in a better harness gives better results — e.g. Sonnet inside Claude Code beats Sonnet inside a weaker harness, largely because of the invisible harness prompt the CLI injects (part of the [[system-prompt]] payload). The working ranking: **CLI > IDE plugin**, because CLIs are easier to unit-test and ship ≈3 months ahead of the plugins (the IDE plugin is the weakest harness). "Which model" (see [[model-hierarchy]]) and "which harness" are two independent knobs. <span style="color:red">Concrete anti-recommendation: *never give your team the GitHub Copilot IDE plugin if you want them to value AI* — it is a GUI layer you can't unit-test, with a slower release cycle. Copilot CLI is acceptable; Claude Code / Codex / Cursor are preferred. The test: can you write an automated test for what the harness will do? If not, you can't iterate on it.</span>

## What lives in the harness

Everything that arrives in the model on turn 1 — see [[system-prompt]] for the full inventory: harness/system prompt, your message and its files, built-in plus MCP [[tool-calling]] schemas, [[claude-md]] memory, and the eager-loaded [[agent-skill]] front-matter. The harness is therefore where [[token-economy]], [[progressive-disclosure]], and [[tripwire]] enforcement all live.

## See also
- [[claude-code-router]]
- [[agents-md]]
- [[system-prompt]]
- [[model-context-protocol]]
- [[agent-skill]]
- [[token-economy]]
- [[model-hierarchy]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
- [[2026-06-23-ai-garmin]]
- [[2026-06-26-ai-agentic-how]]
