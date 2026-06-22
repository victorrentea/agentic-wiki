---
title: Token Economy
category: pattern
tags: [context-window, tool-calling, cost-optimization, agent-workflow]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-10
updated: 2026-06-22
---

The discipline of treating context tokens as a finite, expensive resource — deliberately choosing tools, transport, and restart strategies to avoid burning the [[context-window]] on overhead rather than reasoning.

## Why it matters

Tool definitions are injected into context on every turn. A developer who installs 10+ MCPs can easily accumulate 100+ tool schemas in a fresh conversation before writing a single line of code. Claude Code's `/context` command makes this visible. The practical fix hierarchy:

1. **Prefer CLI over MCP** — a shell command costs near-zero tokens; the equivalent MCP tool drags in its full JSON schema. CLI is often **10x cheaper** in context cost.
2. **Tool-search-tool** — Claude Code's built-in mechanism that loads only tool *names* up front and fetches the full schema on demand. Roughly ≈4k tokens saved per session, at the cost of a slightly higher chance the agent picks the wrong tool.
3. **Watch the first two thoughts + first tool call.** If the agent reaches for a heavy MCP you didn't intend (e.g. opening a browser when you wanted a web fetch), the wrong-tool spiral compounds rapidly. The correct response is `/clear` and restart — sunk cost logic doesn't apply when you can reset for free.

This discipline compounds with the [[ralph-loop]] pattern: each overnight batch enters with a clean context, preserving the model's "smart zone." It also motivates [[profiling-agents]] — the agent that profiled a build suite spent tokens efficiently because its tool surface was tightly scoped. Misconfigured [[model-context-protocol]] servers with dozens of unused tools are the most common culprit.

Two specific tools to weigh on the token budget: [[context7]] injects fresh library docs (useful, but they cost context), and [[claude-code-router]] lets you point the harness at a cheaper provider when the per-token price — not just the count — is what matters.

**Output costs ≈5× input.** You pay for input, but generated tokens are roughly five times more expensive — so the cheapest win is compressing what the model *reads and writes*, not throttling your own reasoning. [[rtk]] proxies dev commands to trim their output (a 97-token `git status` → 19; expect ≈30% overall, not the advertised 80%); [[toon]] reshapes JSON collections into a compact CSV-for-AI form — but benchmark end-to-end, since an unfamiliar format can cost more reasoning than it saves. [[headroom]] is a more evolved sibling of RTK with broader command coverage.

<span style="color:red">**Most tokens live in tool results, not your messages.** File reads, test logs, and stack traces accumulate and ride along on *every* subsequent turn because the LLM is stateless. A single `pom.xml` burns ≈3,000 tokens of XML noise. The pattern: teach the agent once how to extract what it needs from a structured output, then have it write a deterministic script that does the extraction without burning model tokens — the "write yourself a letter" move. This is exactly what [[rtk]] and [[headroom]] do at the CLI level, and what [[agent-skill]] does at the workflow level.</span>

**A conversation is a stateless API.** Every turn re-sends the entire prefix as input; thinking is per-turn and discarded; tool outputs accumulate forever. [[prompt-caching]] makes this affordable — you pay ≈10% for the cached prefix — but the cache TTL is only ≈5 minutes, so an idle fat terminal re-pays full price. The [[effort-setting]] is the other dial: use **xhigh**, never **max**. And don't tax your *own* attention with verbose output (the [[caveman]] anti-pattern) — that bottleneck is scarcer than tokens.

## See also

- [[ralph-loop]]
- [[context-window]]
- [[model-context-protocol]]
- [[profiling-agents]]
- [[tool-calling]]
- [[context7]]
- [[claude-code-router]]
- [[prompt-caching]]
- [[rtk]]
- [[headroom]]
- [[toon]]
- [[effort-setting]]
- [[caveman]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
