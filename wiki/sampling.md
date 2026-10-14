---
title: Sampling
category: concept
tags: [mcp, llm-callback, context-window, token-economy]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-02-ai-insurer]]"]
created: 2026-06-10
updated: 2026-10-13
---

An [[model-context-protocol]] feature that lets a server-side tool call **back into the caller's LLM** to run a sub-prompt — the inverse of a normal tool call.

## Mechanics

In a normal [[tool-calling]] flow the LLM calls a tool and the tool returns data. Sampling reverses the direction: the tool issues a fresh LLM request via the MCP client, using the same model the agent is running on. Crucially, this sub-prompt has **no access to the ongoing conversation context** — it opens a brand-new conversation thread. It burns the caller's tokens and quota. The spec is at [modelcontextprotocol.io/specification/2025-06-18/client/sampling](https://modelcontextprotocol.io/specification/2025-06-18/client/sampling).

## Why It Exists

The motivating problem is [[context-window]] pollution. Consider a tool that fetches a database result set or a web page: dumping 200 raw rows or 50 KB of HTML back into the agent's context is wasteful and pushes useful information toward the lossy middle (see [[token-economy]]). With sampling, the tool runs a condensation sub-prompt — *"here are 200 rows, return the 5 anomalies that matter"* — and hands only the compact summary back to the agent. The agent's context stays lean.

<span style="color:red">A concrete use: your MCP server calls back into the client's LLM so that a **Haiku summarises a Jira issue before it costs you context** [[2026-08-10-ai-consultancy]].</span>

## <span style="color:red">Not to be confused with WebFetch</span>

<span style="color:red">The classic illustration — "fetch 2 MB of HTML, scan it for injection, return 50 KB of markdown" — is actually Claude Code's built-in **WebFetch** tool, which runs your prompt over the page with a small, fast model internally. That is only an *analogy* for sampling: WebFetch is not an MCP server requesting sampling from the client [[2026-08-27-ai-it-services]] [[2026-09-02-ai-insurer]].</span>

<span style="color:red">**Client support is not universal.** The [Claude Code MCP docs](https://code.claude.com/docs/en/mcp) mention elicitation support but not sampling, so check your client before designing a server around it [[2026-09-02-ai-insurer]].</span>

## Contrast with Elicitation

[[elicitation]] pauses a tool to demand **human** input. Sampling pauses a tool to invoke **LLM** input. Both are callbacks mid-tool-call, but in opposite directions: one reaches out to a person, the other reaches back into the model. Neither gives the sub-call access to the parent conversation.

## Caution

Because sampling opens a fresh conversation and spends real tokens with no user visibility, it should be used sparingly and only when the condensation payoff is clear. Overuse makes per-turn costs unpredictable and hard to attribute.

## See also

- [[model-context-protocol]]
- [[elicitation]]
- [[mcp-transport]]
- [[context-window]]
- [[token-economy]]
- [[rag]]
- <span style="color:red">[[model-hierarchy]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
