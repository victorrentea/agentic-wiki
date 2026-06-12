---
title: CLI vs MCP Trade-off
category: concept
tags: [mcp, cli, token-economy, tooling, agent-design]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

When both a CLI and an MCP server exist for the same tool, prefer the CLI if the agent's base model already knows it from training — the CLI costs ≈0 context tokens whereas the MCP floods the context with tool schemas on every session start.

## The token math

An MCP server's tool list is loaded eagerly at session start (part of the [[system-prompt]] payload). For a well-specified MCP, that can run to **300–600 tokens** — before you've typed anything. A CLI the model was trained on costs essentially nothing: the agent already knows the flag syntax; a thin skill wrapper takes ≈60 tokens to remind it of any project-specific conventions.

The [[dynamic-tool-discovery]] mechanism means an unfamiliar MCP still has to load all those schemas even if the agent uses only one or two tools from it.

## When MCP wins

MCP is the right choice when:
- **Auth isolation** matters — the MCP holds credentials the agent never sees directly (see [[secret-zero]]).
- **Sandbox inheritance** matters — a local stdio MCP is a child process that inherits the agent's OS sandbox (see [[mcp-sandbox-inheritance]]).
- The tool has no stable CLI or the CLI isn't in the model's training data, making [[dynamic-tool-discovery]] necessary.
- The tool provides **typed tool definitions** with safe parameter validation.

## When CLI wins

- The CLI is years old and well-represented in training data (e.g. GitHub CLI, Atlassian CLI).
- The agent runs on a developer's machine where bash access is already allowed.
- You want zero eager-load overhead — every token of tool schema is a token stolen from your usable window.

Glean is an example where the MCP / governed-door story wins despite the cost: it's the controlled gateway to sensitive corporate resources (Jira, Slack, codebase search) that you don't want to expose via raw bash.

## See also

- [[token-economy]]
- [[model-context-protocol]]
- [[dynamic-tool-discovery]]
- [[mcp-sandbox-inheritance]]
- [[agent-skill]]
- [[harness]]
- [[2026-06-11-ai-playtika]]
