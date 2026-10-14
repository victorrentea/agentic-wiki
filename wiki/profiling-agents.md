---
title: Profiling Agents
category: pattern
tags: [performance, java, flame-graphs, heap-analysis, build-optimization, observability]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-11-ai-mobile-gaming]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-06-10
updated: 2026-10-13
---

Using an LLM agent as a profiling analyst: feed it Java Flight Recorder traces, async-profiler flame graphs, or binary heap dumps and let it diagnose bottlenecks — without the human needing to read thousands of frames manually.

## What the agent can handle

Three artifact types have proven tractable in practice:

- **Java Flight Recorder (JFR)** — structured event data that JVM emits natively; the agent reads it as structured traces and identifies hot methods, lock contention, and GC pressure.
- **[async-profiler](https://github.com/async-profiler/async-profiler) flame graphs** — SVG or collapsed-stack format; the agent can reason over stack frame aggregates without rendering the graphic.
- **Binary heap dumps (`.hprof`)** — via **[heap_mcp](https://github.com/32kda/heap_mcp_nb)**, an MCP server backed by the NetBeans profiler that converts binary `.hprof` data into agent-readable object histograms and retention paths.

## Real result

Victor cited a pen-test client engagement (the day before the session): the agent profiled a test suite end-to-end and identified enough redundancy and sequencing waste to **cut build time from 45 to 25 minutes** — a 44% improvement found in a single agentic session, where a human engineer had not prioritized it.

## <span style="color:red">More data points</span>

- <span style="color:red">**Heap dumps without source.** Claude diagnosed **17 memory leaks from `.hprof` files alone, with no access to the code** (a Voxxed Days Amsterdam demo); in another run it worked through **14 heap dumps and found every leak in half an hour**; a third found a leak in a heap dump in 20 minutes, again without the code ([[2026-08-31-ai-software-vendor]], [[2026-09-16-ai-safety-critical]], [[2026-07-01-ai-java-community]], [[2026-09-30-ai-enterprise]]). *"No point learning JFR/async-profiler by hand anymore."*</span>
- <span style="color:red">**Tests and pipelines.** An ≈8 h run optimising E2E tests cut their runtime ≈30% ("send minions to explore optimizations, you be the relaxed boss"); a profiler inside backend tests plus a flame graph gave ≈30% in ≈1 h ([[2026-07-01-ai-java-community]]). An open-source project's CI went **42 → 20 min** after agent-injected profilers, caching and parallel tests ([[2026-09-28-ai-gaming-tech]]). Hour-long agent tasks mostly **wait on slow tools** — one build improvement took 2 days for −30% ([[2026-09-30-ai-enterprise]]).</span>
- <span style="color:red">**SQL.** *"Today we're attending the funeral of hand-written SQL."* Models read **execution plans** (and PromQL/Grafana queries) better than humans: a local open-weight model reviewing an old query found 3 major issues, 2 confirmed by the DBAs; a 3-page Oracle query ran **3× faster after one hour**; a local model looping on Oracle `EXPLAIN` plans produced 5 ideas, **2 of which beat expert DBAs** ([[2026-08-06-ai-bank]], [[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]]). The key ingredient each time: a playground schema with **prod-like statistics** / anonymised prod-like data. See [[observability-mcp]] for safe DB access.</span>

## <span style="color:red">The nerd is dead</span>

<span style="color:red">The autumn-2026 framing: **any expertise for which the AI has a [[feedback-loop]] is no longer ethical to do by hand** — profiling (JFR, async-profiler), heap-dump analysis, query tuning, CSS `!important` hunts, Spring best practices. Deep tuning is now the agent's job; yours is building an environment where it can **measure progress** — a benchmark, a prod-like dataset, A/B comparisons, a reference implementation, acceptance tests ([[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]], [[2026-10-07-agentic-reconversion-devoxx]]). So **spend agent tokens first on a faster, stabler pipeline**, not on more features ([[2026-09-28-ai-gaming-tech]]).</span>

<span style="color:red">**Proof, not promise.** A bug fixed the agentic way: reproduce it in a headless browser → a red Playwright test → fix until green → a **subtitled, narrated video** of the fix — $4 of tokens on a 16 GB laptop ([[2026-09-30-ai-enterprise]]). Before reporting a finding upstream, make adversarial sub-agents try to falsify it: an agent-found bug in an identity server (Keycloak) was filed only once they had proved it ([[2026-10-07-agentic-reconversion-devoxx]], [[multi-model-review]]). The agent also teaches back — e.g. don't serialise Spring Data `PageImpl` from a controller; use `pageSerializationMode = VIA_DTO` (Spring Data 3.3+, [docs](https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html#core.web.page.config)) ([[2026-09-30-ai-enterprise]], [[2026-09-28-ai-gaming-tech]]).</span>

## Context and limits

This pattern earns its keep on large, slow builds where humans have deferred profiling due to tooling friction. For small codebases (<50k lines) the agent often reads the source directly and rewrites the bottleneck — profiling tooling can introduce noise by expanding the [[tool-context]] surface (see [[token-economy]]). The agent should be given a tightly scoped tool set: the profiler MCP, read access to source, and nothing else. Pair with a [[ralph-loop]] overnight run for large analyses.

**The "kill all but one" corollary:** when a fleet node misbehaves, keep one malfunctioning instance alive for the autopsy — thread dump, heap dump. A profiling MCP turns that from a specialist ritual into a one-prompt root-cause: *if* you preserved the body first. See [[production-safety]].

## See also

- [[token-economy]]
- [[ralph-loop]]
- [[tool-context]]
- [[tool-calling]]
- [[observability-mcp]]
- [[production-safety]]
- <span style="color:red">[[feedback-loop]]</span>
- <span style="color:red">[[multi-model-review]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-11-ai-mobile-gaming]]
