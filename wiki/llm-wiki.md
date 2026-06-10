---
title: LLM Wiki
category: tool
tags: [knowledge-management, obsidian, rag, compounding-artifact, legacy-codebases]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Andrej Karpathy's pattern: instead of re-retrieving knowledge on every query, an LLM incrementally builds and maintains a persistent, interlinked markdown wiki — a compounding artifact that grows more useful with each session.

## The pattern

Described in [Karpathy's gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), the core idea is that re-deriving knowledge from raw sources on every query is wasteful and lossy. Instead, a curator agent reads new source material (transcripts, articles, docs) and writes or updates wiki pages in a structured vault, cross-linking each new page to related existing ones. The result compounds: a page written from session 1 becomes a link target in session 10, accumulating context no single query could reconstruct.

[Obsidian](https://obsidian.md/) is the natural viewer — free, plain-git, phone-syncable, and its graph view makes the link topology visible in a way that reveals gaps and clusters. Victor's framing: *"skills on steroids, but linked."*

## Relationship to other patterns

LLM Wiki **complements [[rag]]**, not replaces it: RAG retrieves from unstructured corpora; a wiki is pre-synthesized, explicitly linked, and human-curated at the source layer. For legacy codebases, the pattern is especially powerful — map the monolith **module-by-module**, one wiki page per subsystem, and an agent navigating that wiki avoids the [[context-window]] explosion of reading millions of lines cold. The open question is recall at scale: how does a new page get linked to something written 200 sessions ago? A pragmatic answer is [[vector-search]]-assisted link suggestion (semantic neighbors across the corpus), followed by agent confirmation — hybrid [[rag]] + graph.

**This repository is itself an instance of this pattern.** Source transcripts live in `raw/`; the LLM owns `wiki/`.

## See also

- [[rag]]
- [[vector-search]]
- [[ralph-loop]]
- [[context-window]]
- [[code-graph]]
- [[2026-06-10-spring-ai-itkonekt]]
