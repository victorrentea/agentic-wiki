---
title: MCP Sandbox Inheritance
category: concept
tags: [mcp, security, sandbox, local-stdio, child-process]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

When an MCP server runs as a **local stdio** child process, it inherits the agent's OS sandbox — a meaningful security property that remote MCPs do not share.

## The inheritance property

A local (stdio) MCP is **spawned by the agent** and communicates over stdin/stdout. Because it is a child process, the OS sandbox applied to the agent automatically applies to the spawned MCP too: if the agent is constrained to read only a specific folder and contact only certain hosts, so is every MCP it launches. There is no separate containment to configure.

A **remote MCP** (SSE/HTTP) runs on a different machine or process — it is a network peer, not a child. The agent's sandbox does not extend to it. A remote MCP needs its own authentication, authorization, and rate-limiting because *it lives outside your trust boundary*.

## Why this matters for security

The key architectural insight: pick local stdio MCPs for tools you want contained (e.g. Playwright, code-graph, database readers) and treat remote MCPs as external services that require the same security posture you apply to any API call. See [[os-sandbox]] for the underlying OS primitives and [[docker-sandboxing]] for the gold standard.

The corollary: **never mount the Docker socket** inside the agent's container, because any MCP (or bash call) that can reach the socket gets root-equivalent access to the host, defeating the sandbox entirely.

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[os-sandbox]]
- [[docker-sandboxing]]
- [[agent-permissions]]
- [[2026-06-11-ai-playtika]]
