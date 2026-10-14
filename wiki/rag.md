---
title: Retrieval-Augmented Generation
category: concept
tags: [rag, vector-search, spring-ai, advisor, knowledge]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-07-09-ai-live-casino]]"]
created: 2026-06-10
updated: 2026-10-13
---

RAG (Retrieval-Augmented Generation) lets an agent **pull** external knowledge on demand rather than requiring you to push everything into the [[system-prompt]] or CLAUDE.md upfront.

In [[spring-ai]], RAG is implemented as a `QuestionAnswerAdvisor` — an [[advisor]] that intercepts the prompt, queries a [[vector-search|vector store]] for semantically similar documents, and injects the results before the call reaches the model. See the [Spring AI RAG reference](https://docs.spring.io/spring-ai/reference/api/retrieval-augmented-generation.html) for wiring details.

**Demo from [[2026-06-10-spring-ai-open-workshop]]:** the query "my cat's breath stinks and it has a broken tooth" returned the *dental* veterinary specialty with no keyword overlap — pure semantic match.

**Prefer pointers over verbatim text.** Instead of dumping the source document into the context, have RAG return a doc ID, URL, or wiki page slug and let the agent fetch it dynamically. This keeps answers fresh and avoids duplicating content.

**The drift problem** is the main operational risk: when the source row changes, its embedding is stale until re-vectorized. The strongest fix is to update source and vector in the same database transaction (e.g. with [[pgvector]]). Async re-embed with eventual consistency is acceptable too — just decide consciously and put a freshness SLA on it.

<span style="color:red">**RAG poisoning** is the security twin of drift: whatever lands in the store is retrieved later as *trusted* knowledge. A rogue employee writes a few poisoned Confluence pages, the nightly job embeds them, and weeks later the agent retrieves "drop the prod schema to fix this" as company procedure. Treat the corpus as untrusted input — it is an indirect [[prompt-injection]] channel with persistence, the same shape as [[memory-poisoning]] — and keep the agent's destructive tools behind gates that no retrieved text can open (the "untrusted content" leg of the [[lethal-trifecta]]).</span>

For richer retrieval, combine semantic similarity with fuzzy/exact signals: see [[vector-search]] for the hybrid approach.

**RAG = approximate find** ("search for a shoe, find a slipper") — it is the AI's fuzzy search over company knowledge. But after it *finds* a document it tends to load a whole paragraph or wiki page → token burn, so spin a summarization [[sub-agents|sub-agent]] to extract the essence of a human-written Confluence/Jira page before feeding it in. The richer, pre-synthesized cousin is the [[llm-wiki]]: **graph traversal** of cross-linked concept pages versus RAG's **vector similarity** — two complementary ways to navigate massive knowledge. (For *code* reuse, fuzzy vector find is the wrong tool — grep the business symbol names and walk the [[code-graph]] instead.)

## See also
- [[vector-search]]
- [[pgvector]]
- [[advisor]]
- [[context-window]]
- [[llm-wiki]]
- [[sub-agents]]
- <span style="color:red">[[memory-poisoning]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[lethal-trifecta]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- <span style="color:red">[[2026-07-09-ai-live-casino]]</span>
