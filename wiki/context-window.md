---
title: Context Window
category: concept
tags: [context-window, token-economy, lost-in-the-middle, memory, performance]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

The context window is the finite token budget a model sees in one call; bigger is not always better — it costs money, degrades quality on cheap models, and suffers from "lost in the middle."

## Why cap what you send

Three independent reasons to keep context lean:

1. **Cost.** Every token in and out is billed. [[chat-memory]] defaults to a window of 6 messages in the demo (≈60 in production) for exactly this reason.
2. **The "dumb zone."** Small, fast models (GPT-4o-mini class) have limited effective context. Flood them and answer quality collapses — the model appears to get dumber.
3. **Lost in the middle.** Research ([arxiv 2307.03172](https://arxiv.org/abs/2307.03172)) shows models attend best to the **start and end** of the context; information buried in the middle is reliably degraded even in models marketed as long-context.

## Tool definitions eat tokens silently

[[tool-calling]] schemas are injected into the context on every turn. A workspace with 100+ MCP tools can consume thousands of tokens before a single user message. The Claude Code "tool-search-tool" loads only tool *names* by default and fetches full schemas on demand — trading a small accuracy risk for significant token savings. See [[token-economy]].

## Implications for [[chat-memory]]

The sliding window in [[chat-memory]] is the primary defense against context bloat. Production systems augment it with **summarization / compaction** of evicted turns so the bot retains a gist of early context without re-sending the raw messages. [[rag]] is the complementary strategy: pull relevant knowledge on demand rather than pre-loading it.

Source: [[2026-06-10-spring-ai-itkonekt]]

External: ["Lost in the Middle" paper](https://arxiv.org/abs/2307.03172)

## See also

- [[chat-memory]]
- [[advisor]]
- [[token-economy]]
- [[rag]]
- [[tool-calling]]
- [[system-prompt]]
