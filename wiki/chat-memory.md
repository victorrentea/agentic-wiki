---
title: Chat Memory
category: concept
tags: [spring-ai, memory, context-window, advisor, stateless]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Chat memory is a **sliding window** of the last N messages that is re-sent to the LLM on every call, compensating for the fact that LLM APIs are stateless.

## Why it exists

OpenAI, Anthropic, and every other LLM API treats each HTTP call as independent — the model sees only what you send in the current request. "Memory" is an illusion your application constructs by prepending recent conversation turns to each new prompt.

## How it works in Spring AI

The window size is a configuration parameter (the demo used **6 messages**; production typically uses ≈60). Spring AI implements this as an [[advisor]] (`MessageChatMemoryAdvisor`): before the LLM call it injects the last N messages; after the call it appends the new exchange and evicts the oldest. Older turns "grey out" — they remain in [[chat-history]] but are no longer fed to the model, so the bot genuinely cannot answer "what was the first thing I said?"

See the official docs: [Spring AI Chat Memory](https://docs.spring.io/spring-ai/reference/api/chat-memory.html)

## The name-vs-id bug

The memory map is keyed per owner. In the live demo, messages were stored under `ownerId` but retrieved under `name` — users briefly saw each other's chat. The lesson: always use a stable, non-guessable identifier (id, not display name) as the memory key.

## Why cap the window

- [[context-window]] space is finite and expensive.
- Cheap/fast models slide into the "dumb zone" when flooded with tokens.
- [[context-window#lost-in-the-middle]] — models attend best to the start and end of context; buried middle content degrades recall.

Production systems often add **summarization / compaction** of evicted turns to avoid abrupt amnesia.

Source: [[2026-06-10-spring-ai-itkonekt]]

## See also

- [[chat-history]]
- [[advisor]]
- [[context-window]]
- [[chat-model]]
- [[system-prompt]]
