---
title: Graph Database
category: concept
tags: [graph-database, code-graph, llm-wiki, knowledge-graph, agent-tools]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A graph database stores data as nodes and explicit typed edges, letting agents traverse structural connections rather than relying on keyword grep or semantic similarity.

Two "weird DB" types are especially useful for agents: [[vector-search]] (finds items by meaning) and graph (follows declared relationships). They solve different problems and often complement each other.

**[[code-graph]]** is a concrete example: it maintains a continuously-synced symbol graph over a codebase, updated by a file-system watcher within ≈1 second of each save. A query for "exact callers of `findById`" returns 7 precise call sites with line numbers; `grep` returns 39 noisy matches and burns tokens toward the dumb zone. Code Graph also supports untested-method detection (any method with no test anywhere in its call-graph subtree) and transitive blast-radius analysis that crosses module and language boundaries. From [[2026-06-10-spring-ai-itkonekt]]: it's most valuable on large modular monoliths; on codebases under ≈50k lines, a frontier model can read everything directly and extra navigation tools can actually hurt.

**[[llm-wiki]]** is the other live example: an agent-curated knowledge base where each page links explicitly to related concepts. Obsidian's graph view visualizes the link structure. Here too, the graph carries meaning that flat-file search cannot: the agent can traverse "what does this concept depend on?" structurally instead of re-reading the whole corpus.

A pragmatic hybrid: use [[vector-search]] to suggest candidate links across the corpus (semantic recall), then confirm them via graph traversal (structural precision).

## See also
- [[code-graph]]
- [[llm-wiki]]
- [[vector-search]]
- [[rag]]
- [[tool-calling]]
- [[2026-06-10-spring-ai-itkonekt]]
