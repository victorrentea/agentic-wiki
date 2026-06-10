---
title: Vector Search
category: concept
tags: [vector-search, embeddings, rag, semantic-similarity, hybrid-rag]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Vector search finds semantically similar content by comparing dense floating-point embeddings via cosine similarity rather than matching keywords.

Text (or images) are passed through an embedding model that maps meaning into a high-dimensional vector. Words and phrases with related meaning cluster nearby in that space — "shoe" and "slipper" land close together even though they share no characters. Cosine similarity measures the angle between two vectors, returning a score regardless of magnitude.

**Embeddings are GPU-expensive.** Running an embedding model at scale requires dedicated hardware; Romania's largest e-commerce site maintains an in-house GPU cluster to continuously embed product text and images. This is a real infrastructure cost to plan for.

**Hybrid RAG** is where production systems live. Pure semantic search misses typos (Levenshtein distance handles those), synonyms, and cross-language terms. A hybrid pipeline runs semantic and fuzzy/exact signals in parallel and merges them by confidence score. As noted in [[2026-06-10-spring-ai-itkonekt]]: *"if a student needs a PhD topic — hybrid ranking."*

In [[spring-ai]], embeddings are generated via an `EmbeddingModel` and stored in a vector store (see [[pgvector]] for the Postgres option). The [[rag]] `QuestionAnswerAdvisor` queries the store on each turn. For the drift problem — keeping embeddings in sync with their source — see [[pgvector]].

[[graph-database]] is the complementary "weird DB": where vector search finds items by meaning, a graph traverses explicit structural connections.

## See also
- [[rag]]
- [[pgvector]]
- [[graph-database]]
- [[advisor]]
- [[context-window]]
- [[2026-06-10-spring-ai-itkonekt]]
