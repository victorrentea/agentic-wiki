---
title: Code Graph
category: tool
tags: [code-graph, symbol-graph, static-analysis, monolith, blast-radius]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
created: 2026-06-10
updated: 2026-06-11
---

Code Graph is an npm-installable symbol-graph tool that maintains an always-current, file-watcher-synced index of every call site, type, and definition in a codebase — reindexing within ≈1 second of a file save.

The killer demo at [[2026-06-10-spring-ai-itkonekt]]: *"who calls `findById` on the owner repository?"* — `grep` returns **39 noisy matches** spanning every unrelated repository and floods the [[context-window]] toward the "dumb zone"; Code Graph returns the **exact 7 call sites with line numbers**, then **6** after Victor deleted one and re-ran, confirming live reindexing. That precision gap is the entire argument: fewer tokens, higher signal, no false positives burning budget.

**Star features:**

1. **Untested-method detection** — any method with no test on any leaf of its call graph surfaces as a warning (no green test leaf). An agent can audit coverage without running the suite.
2. **Cross-language blast-radius** — transitive impact analysis that follows call edges across module and language boundaries, which grep structurally cannot do. Surfaced at least one plausible false positive during the demo — a healthy reminder to verify graph-mined facts before acting on them.

**When it earns its keep:** large modular monoliths where no single person holds the full call graph in memory. For codebases under ≈50k lines, Opus can read the whole service and reason over it directly — adding extra navigation tools can actually hurt by presenting too many choices. Code Graph pays off when the [[vector-search]]-style "read everything" approach breaks down.

It pairs naturally with [[rag]] for hybrid retrieval: exact symbol edges from the graph + semantic proximity from vectors.

<span style="color:red">A symbol graph is also the "grep-for-reuse" defense against the agent reinventing what already exists — better to let the agent **discover in the code** than to paste a snippet into a chat that doesn't know your POM (a [[hallucination]] cause). Note the failure mode: past ≈600k tokens an agent in "context anxiety" *stops* checking for duplication ([[vibe-coding]]) — precisely when an exact reuse lookup matters most.</span>

## See also
- [[rag]]
- [[vector-search]]
- [[context-window]]
- [[token-economy]]
- [[profiling-agents]]
- [[llm-wiki]]
- [[hallucination]]
- [[vibe-coding]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
