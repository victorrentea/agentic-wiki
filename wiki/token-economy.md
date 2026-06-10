---
title: Token Economy
category: pattern
tags: [context-window, tool-calling, cost-optimization, agent-workflow]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

The discipline of treating context tokens as a finite, expensive resource — deliberately choosing tools, transport, and restart strategies to avoid burning the [[context-window]] on overhead rather than reasoning.

## Why it matters

Tool definitions are injected into context on every turn. A developer who installs 10+ MCPs can easily accumulate 100+ tool schemas in a fresh conversation before writing a single line of code. Claude Code's `/context` command makes this visible. The practical fix hierarchy:

1. **Prefer CLI over MCP** — a shell command costs near-zero tokens; the equivalent MCP tool drags in its full JSON schema. CLI is often **10x cheaper** in context cost.
2. **Tool-search-tool** — Claude Code's built-in mechanism that loads only tool *names* up front and fetches the full schema on demand. Victor estimates ≈4k tokens saved per session, at the cost of a slightly higher chance the agent picks the wrong tool.
3. **Watch the first two thoughts + first tool call.** If the agent reaches for a heavy MCP you didn't intend (e.g. opening a browser when you wanted a web fetch), the wrong-tool spiral compounds rapidly. The correct response is `/clear` and restart — sunk cost logic doesn't apply when you can reset for free.

This discipline compounds with the [[ralph-loop]] pattern: each overnight batch enters with a clean context, preserving the model's "smart zone." It also motivates [[profiling-agents]] — the agent that profiled a build suite spent tokens efficiently because its tool surface was tightly scoped. Misconfigured [[model-context-protocol]] servers with dozens of unused tools are the most common culprit.

Two specific tools to weigh on the token budget: [[context7]] injects fresh library docs (useful, but they cost context), and [[claude-code-router]] lets you point the harness at a cheaper provider when the per-token price — not just the count — is what matters.

## See also

- [[ralph-loop]]
- [[context-window]]
- [[model-context-protocol]]
- [[profiling-agents]]
- [[tool-calling]]
- [[context7]]
- [[claude-code-router]]
- [[2026-06-10-spring-ai-itkonekt]]
