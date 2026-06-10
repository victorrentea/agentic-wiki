---
title: Chat History
category: concept
tags: [spring-ai, history, transcript, server-side, stateless]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Chat history is the verbatim, full transcript of every message ever exchanged in a conversation, stored server-side and independent of what the LLM actually receives.

## How it works

In the Spring AI demo, history lives in a plain Spring bean — a `Map<ownerId, List<Message>>` held in process memory. Every user message and every assistant reply is appended unconditionally. This is what re-renders when the user refreshes the browser: the full conversation is intact because it comes from the server, not the browser.

**Lifecycle:** survives a browser refresh; dies on application redeploy (it is in-memory, not persisted to a database). For production, this map should be backed by Redis, a relational table, or similar.

## Chat history vs chat memory

These two terms are easy to conflate:

| | Chat History | [[chat-memory]] |
|---|---|---|
| Scope | Every message ever | Last N messages |
| Consumer | UI / audit log | The LLM on each call |
| Storage | Server-side map | Derived from history |
| Survives refresh | Yes | Yes (re-derived) |
| Survives redeploy | Only if persisted | Only if history persists |

The [[advisor]] layer reads from history to build the sliding-window subset that becomes [[chat-memory]].

## Identity key

Like [[chat-memory]], history is keyed per owner. Using `name` instead of `id` as the key caused a live data-leakage bug during the demo — see [[chat-memory]] for details.

Source: [[2026-06-10-spring-ai-itkonekt]]

## See also

- [[chat-memory]]
- [[advisor]]
- [[chat-model]]
- [[context-window]]
- [[system-prompt]]
