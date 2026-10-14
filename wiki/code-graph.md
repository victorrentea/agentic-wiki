---
title: Code Graph
category: tool
tags: [code-graph, symbol-graph, static-analysis, monolith, blast-radius, lsp]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-10
updated: 2026-10-13
---

Code Graph is an npm-installable symbol-graph tool that maintains an always-current, file-watcher-synced index of every call site, type, and definition in a codebase — reindexing within ≈1 second of a file save.

The killer demo at [[2026-06-10-spring-ai-open-workshop]]: *"who calls `findById` on the owner repository?"* — `grep` returns **39 noisy matches** spanning every unrelated repository and floods the [[context-window]] toward the "dumb zone"; Code Graph returns the **exact 7 call sites with line numbers**, then **6** after Victor deleted one and re-ran, confirming live reindexing. <span style="color:red">Later sessions reproduced the gap on other codebases — 47 grep hits vs 3 real callers, 85 vs 13, 115 vs 4, 120 vs 8: "the agent searches with grep — text, not syntax." The IDE has a parser and an AST; the agent, by default, doesn't.</span>

## <span style="color:red">Precision is not token savings</span>

<span style="color:red">The original pitch was "fewer tokens, higher signal". By late summer 2026 the measurements said otherwise. On a 1M-line project, an agent forced to use a code-graph/LSP tool ran its own benchmark and found ≈20% **more** tokens spent than with the native Grep tool ("grep on steroids") — "disable it, master, it gets in my way" — and it won the debate. In another session Claude refused the code-graph MCP after measuring ≈30% more tokens than grep. A third source put it bluntly: a code graph does *not* save tokens; its value is a **programmatic call graph over legacy** (a 10M-line monolith).</span>

<span style="color:red">So: **don't "add and pray"**. Don't underestimate grep; run the same task repeatedly on *your* project (for weeks, not once) and measure before adopting a navigation tool — the authors' study on their test project says nothing about yours ([[optimize-then-measure-subset]], [[token-economy]]). Re-measure now that Claude Code ships an [LSP tool via plugins](https://code.claude.com/docs/en/plugins/code-intelligence).</span>

## Star features

1. **Untested-method detection** — any method with no test on any leaf of its call graph surfaces as a warning (no green test leaf). An agent can audit coverage without running the suite.
2. **Cross-language blast-radius** — transitive impact analysis that follows call edges across module and language boundaries, which grep structurally cannot do. Surfaced at least one plausible false positive during the demo — a healthy reminder to verify graph-mined facts before acting on them.
3. <span style="color:red">**Branches per flow** — with a graph you can sum the branching (cyclomatic complexity) transitively along the call chain from every entry point (REST, Kafka listeners, MCP tools): "this endpoint has 23 branches, so at least 23 tests" — impossible to do mentally. In a PR it shows as "+1" per new `if` on a hot path, and flags when a change doubles a flow's complexity.</span>

**When it earns its keep:** large modular monoliths where no single person holds the full call graph in memory. For codebases under ≈50k lines, Opus can read the whole service and reason over it directly — adding extra navigation tools can actually hurt by presenting too many choices. Code Graph pays off when the [[vector-search]]-style "read everything" approach breaks down.

It pairs naturally with [[rag]] for hybrid retrieval: exact symbol edges from the graph + semantic proximity from vectors.

## <span style="color:red">Alternatives and cheaper options</span>

- <span style="color:red">**The IDE's own index.** Run Claude Code from the IntelliJ terminal and link it with `/ide`: the agent gets ctrl-click semantics and the IDE's inspections ("find references", never-called methods) without a separate graph ([LSP](https://microsoft.github.io/language-server-protocol/)). IntelliJ Structural Search also queries syntax, not text.</span>
- <span style="color:red">**An MCP over the language server** — e.g. [mcp-language-server](https://github.com/isaacphi/mcp-language-server) gives real find-usages exactly like the IDE, without a graph database.</span>
- <span style="color:red">[**Serena**](https://github.com/oraios/serena) — an MCP with IDE-like **symbol-level** navigation (call hierarchy, find references, go-to-definition), LSP-backed. A better fit than vector search for verbose Java/C#, where embeddings blur meaning but symbols are exact. (One session listed it as RAG/semantic search; symbol navigation is the accurate description.)</span>
- <span style="color:red">[**CodeGraphContext**](https://github.com/CodeGraphContext/CodeGraphContext) — indexes the codebase into a graph database of functions, classes and call hierarchies served as agent context, kept in sync by filesystem hooks; [CodeGraph](https://github.com/colbymchenry/codegraph) is another pre-indexed call-graph option.</span>
- <span style="color:red">When flat markdown memory stops scaling, [Graphiti](https://github.com/getzep/graphiti) builds real-time knowledge graphs for agents (see [[graph-database]]); Martin Fowler's site discusses [context anchoring](https://www.martinfowler.com/articles/reduce-friction-ai/context-anchoring.html).</span>

## Reuse hunting

A symbol graph is also the "grep-for-reuse" defense against the agent reinventing what already exists — better to let the agent **discover in the code** than to paste a snippet into a chat that doesn't know your POM (a [[hallucination]] cause). Note the failure mode: past ≈600k tokens an agent in "context anxiety" *stops* checking for duplication ([[vibe-coding]]) — precisely when an exact reuse lookup matters most. <span style="color:red">Agents also lack the human intuition that "this should already exist somewhere": they grep, find nothing, and edit their own copy — which then [[drift|drifts]]. Code style is irrelevant now; **code design remains ours**.</span>

**Reuse hunting must be instructed explicitly.** Before writing new code a "reuse" agent should look for an existing implementation, but left alone an anxious agent YOLOs and skips the check. The durable technique in a big codebase: **grep the function/symbol names tied to the business concept**, plus **code-graph for syntax-level navigation** — and you must tell the agent, in so many words, to do it. This is part of [[code-review]]'s reuse/duplication concern in a [[multi-model-review]] quorum.

## Dead code

For legacy-code migration, Code Graph is especially valuable at the **dead-code detection** stage: a method with no callers in the graph has no live callers, and can be safely deleted. Combining this with coverage data from a test suite gives a "definitely dead" signal that grep cannot provide. See [[legacy-migration-stages]] for the full recipe. <span style="color:red">Without such an oracle (graph, LSP or IDE inspections) the agent is afraid to delete and leaves duplicates behind — see [[ai-never-deletes]].</span>

## See also
- [[rag]]
- [[vector-search]]
- [[context-window]]
- [[token-economy]]
- [[profiling-agents]]
- [[llm-wiki]]
- [[hallucination]]
- [[vibe-coding]]
- [[code-review]]
- [[multi-model-review]]
- [[legacy-migration-stages]]
- [[ai-never-deletes]]
- [[graph-database]]
- [[optimize-then-measure-subset]]
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
