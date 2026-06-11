---
title: Prompt Caching
category: concept
tags: [token-economy, cost-optimization, context-window, stateless-api, cache]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Prompt caching is the provider-side mechanism that stores the conversation prefix you already sent so that re-sending it on the next turn costs a fraction of full input price — both the savior and the trap of the [[token-economy]].

## Why it exists

A conversation is a **stateless API**: every turn re-sends *everything before you* as input tokens, prefixed to your new prompt. So one tiny new token on top of a 65k-token history still pays for the whole prefix. (Thinking is per-turn and thrown away; tool calls and their outputs accumulate forever.) Caching the unchanged prefix is what makes long agentic sessions affordable — you pay ≈**10%** for the cached portion.

## The 5-minute trap

The cache has a short TTL. It was 1 hour until ≈April, then silently dropped to **5 minutes** — and people saw a 5× burn. If your agent asks a question and you reply more than 5 minutes later, the cache has expired and you **re-pay full price** for the entire prefix. Cache *writes* also cost ≈1.25× base (the opt-in 1-hour cache ≈2×), so caching only wins when you genuinely reuse the prefix soon.

The canonical failure: **four terminals open**, two of them carrying 500k of accumulated context, all neglected past the cache window. Idle fat terminals are the worst case both for the cache *and* for the [[dumb-zone]] — *"you're not a 10× developer, you're a 10× sucker."*

## See also
- [[token-economy]]
- [[context-window]]
- [[dumb-zone]]
- [[plan-mode]]
- [[ai-addiction]]
- [[2026-06-11-ai-playtika]]
