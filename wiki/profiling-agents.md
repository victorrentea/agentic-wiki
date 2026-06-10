---
title: Profiling Agents
category: pattern
tags: [performance, java, flame-graphs, heap-analysis, build-optimization]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Using an LLM agent as a profiling analyst: feed it Java Flight Recorder traces, async-profiler flame graphs, or binary heap dumps and let it diagnose bottlenecks — without the human needing to read thousands of frames manually.

## What the agent can handle

Three artifact types have proven tractable in practice:

- **Java Flight Recorder (JFR)** — structured event data that JVM emits natively; the agent reads it as structured traces and identifies hot methods, lock contention, and GC pressure.
- **[async-profiler](https://github.com/async-profiler/async-profiler) flame graphs** — SVG or collapsed-stack format; the agent can reason over stack frame aggregates without rendering the graphic.
- **Binary heap dumps (`.hprof`)** — via **[heap_mcp](https://github.com/32kda/heap_mcp_nb)**, an MCP server backed by the NetBeans profiler that converts binary `.hprof` data into agent-readable object histograms and retention paths.

## Real result

Victor cited a pen-test client engagement (the day before the session): the agent profiled a test suite end-to-end and identified enough redundancy and sequencing waste to **cut build time from 45 to 25 minutes** — a 44% improvement found in a single agentic session, where a human engineer had not prioritized it.

## Context and limits

This pattern earns its keep on large, slow builds where humans have deferred profiling due to tooling friction. For small codebases (<50k lines) the agent often reads the source directly and rewrites the bottleneck — profiling tooling can introduce noise by expanding the [[tool-context]] surface (see [[token-economy]]). The agent should be given a tightly scoped tool set: the profiler MCP, read access to source, and nothing else. Pair with a [[ralph-loop]] overnight run for large analyses.

## See also

- [[token-economy]]
- [[ralph-loop]]
- [[tool-context]]
- [[tool-calling]]
- [[2026-06-10-spring-ai-itkonekt]]
