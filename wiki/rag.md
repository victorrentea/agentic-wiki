---
title: Retrieval-Augmented Generation
category: concept
tags: [rag, vector-search, spring-ai, advisor, knowledge]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

RAG (Retrieval-Augmented Generation) lets an agent **pull** external knowledge on demand rather than requiring you to push everything into the [[system-prompt]] or CLAUDE.md upfront.

In [[spring-ai]], RAG is implemented as a `QuestionAnswerAdvisor` — an [[advisor]] that intercepts the prompt, queries a [[vector-search|vector store]] for semantically similar documents, and injects the results before the call reaches the model. See the [Spring AI RAG reference](https://docs.spring.io/spring-ai/reference/api/retrieval-augmented-generation.html) for wiring details.

**Demo from [[2026-06-10-spring-ai-itkonekt]]:** the query "my cat's breath stinks and it has a broken tooth" returned the *dental* veterinary specialty with no keyword overlap — pure semantic match.

**Prefer pointers over verbatim text.** Instead of dumping the source document into the context, have RAG return a doc ID, URL, or wiki page slug and let the agent fetch it dynamically. This keeps answers fresh and avoids duplicating content.

**The drift problem** is the main operational risk: when the source row changes, its embedding is stale until re-vectorized. The strongest fix is to update source and vector in the same database transaction (e.g. with [[pgvector]]). Async re-embed with eventual consistency is acceptable too — just decide consciously and put a freshness SLA on it.

For richer retrieval, combine semantic similarity with fuzzy/exact signals: see [[vector-search]] for the hybrid approach.

## See also
- [[vector-search]]
- [[pgvector]]
- [[advisor]]
- [[context-window]]
- [[2026-06-10-spring-ai-itkonekt]]
