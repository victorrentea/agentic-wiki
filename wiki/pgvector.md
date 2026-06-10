---
title: pgvector
category: tool
tags: [pgvector, vector-search, postgres, rag, drift]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

pgvector is an open-source extension that adds vector similarity search to PostgreSQL, letting you store embeddings in the same database as your application data.

Source: [https://github.com/pgvector/pgvector](https://github.com/pgvector/pgvector)

The key operational advantage is transactional consistency: you can **update the source row and its embedding vector in a single Postgres transaction**. This eliminates the drift window that plagues async re-vectorization pipelines, where a changed source row can serve a stale embedding until a background job catches up. In [[rag]], a stale embedding means the retrieval step silently returns outdated results — users see the old answer even though the underlying data changed.

In [[spring-ai]], pgvector integrates as a `VectorStore` bean; the `QuestionAnswerAdvisor` (see [[advisor]] and [[rag]]) queries it on every prompt. The workshop described in [[2026-06-10-spring-ai-itkonekt]] used a **file-based JSON vector store** instead, purely to avoid requiring Docker Desktop for attendees — pgvector is the recommended production choice.

**Drift mitigation options, strongest to weakest:**
1. Update source + vector in one transaction (pgvector native, strongest).
2. Async re-embed with a freshness SLA and stale-data monitoring (acceptable, "a bit off like Elasticsearch").

Because vectors live in Postgres alongside relational data, you can join embeddings with your existing tables, apply row-level security, and use standard Postgres backups — no separate vector infrastructure to operate.

## See also
- [[rag]]
- [[vector-search]]
- [[advisor]]
- [[spring-ai]]
- [[2026-06-10-spring-ai-itkonekt]]
