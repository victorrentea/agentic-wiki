---
title: Index
category: source
tags: [index, catalog]
updated: 2026-06-10
---

# Index

Catalog of all wiki pages, grouped by category. Start at [[overview]] for the conceptual map. Read order and provenance live in [[log]].

## Concepts — chat fundamentals
- [[chat-model]] — Spring AI's provider-agnostic `ChatModel`; swap OpenAI ↔ local via a Spring profile.
- [[chat-memory]] — the sliding window of recent messages re-sent on every call.
- [[chat-history]] — the verbatim full transcript stored server-side.
- [[advisor]] — Spring AI interceptor (≈ aspect) that runs before/after the LLM call.
- [[context-window]] — why bigger context isn't better (cost, "dumb zone", lost-in-the-middle).
- [[system-prompt]] — persona + guardrails that never leave the window; the four message types.

## Concepts — MCP & knowledge
- [[model-context-protocol]] — open protocol connecting agents to tools/data; resources, tools, prompts.
- [[mcp-transport]] — SSE + POSTs, JSON-RPC correlation, Streamable HTTP.
- [[elicitation]] — server forces human-in-the-loop approval mid-tool-call.
- [[sampling]] — a tool calls back into the caller's LLM to condense data.
- [[rag]] — retrieval-augmented generation: pull extra knowledge into the context.
- [[vector-search]] — semantic similarity via cosine over embeddings; hybrid RAG.
- [[graph-database]] — explicit-connection knowledge graphs (vs vector similarity).

## Security
- [[prompt-injection]] — jailbreaks, sycophancy, multi-turn drift.
- [[judge-llm]] — a cheap classifier LLM screens prompt and response.
- [[guardrails]] — the layered defense stack, weakest to strongest.
- [[tool-calling]] — the model invents tool parameters; harden and validate them.
- [[tool-context]] — pass identity out-of-band, never as model-authored data.
- [[jwt-identity]] — parse and validate the access token before trusting it.
- [[supply-chain-attack]] — poisoned dependencies and the agent-era twist.
- [[dual-leg-rule]] — confidential data XOR external reach; cut one leg.

## Tools & frameworks
- [[spring-ai]] — the Java framework the whole workshop is built on.
- [[ollama]] — run open LLMs locally (Qwen 2.5) for data residency.
- [[embabel]] — Rod Johnson's JVM agent-workflow framework.
- [[code-graph]] — file-watcher-synced code symbol graph.
- [[context7]] — MCP that injects up-to-date library docs.
- [[pgvector]] — Postgres vector-similarity extension.
- [[claude-code-router]] — route the CloudCode harness to other model providers.
- [[grill-me]] — a skill that interrogates an idea with probing questions.
- [[llm-wiki]] — Karpathy's pattern that *this* repository instantiates.

## Patterns — operating agents
- [[ralph-loop]] — spec by day, run agent loops overnight with clean context.
- [[token-economy]] — treat context tokens as a finite, expensive resource.
- [[profiling-agents]] — feed JFR / flame graphs / heap dumps to agents.

## Sources
- [[2026-06-10-spring-ai-itkonekt]] — Spring AI @ ITKonekt workshop summary (seed source).
