---
title: Context7
category: tool
tags: [context7, mcp, documentation, rag, library-docs, knowledge-cutoff]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-10
updated: 2026-06-23
---

[Context7](https://github.com/upstash/context7) is an [[model-context-protocol]] server that injects version-specific, up-to-date library documentation into the active prompt at query time — "a Wikipedia for docs, made for LLMs."

LLM training cutoffs are a hard constraint: a model trained through early 2025 has no knowledge of a library released or significantly updated afterward. The standard workaround — pasting docs into the prompt manually — is tedious, token-expensive, and still delivers the wrong version if you paste carelessly. Context7 automates this: when an agent needs documentation for a specific library version, it calls the Context7 MCP tool, which fetches and returns the relevant reference material directly, scoped to the version in use.

This is a form of on-demand [[rag]] applied to the documentation corpus rather than a user's own data. The retrieval is version-aware, which distinguishes it from a generic web search — you get the exact API surface for Spring AI 1.0.0-M6, not a mix of blog posts and outdated Javadoc.

**Practical value:** any agent working on a project that uses libraries newer than the model's training cutoff should have Context7 wired in. This includes rapidly evolving ecosystems like [[spring-ai]] itself (which was pre-GA at time of the workshop), MCP tooling, and any framework with frequent breaking changes. It also reduces [[prompt-injection]] surface compared to raw WebFetch, since Context7 controls the source corpus.

**Token cost awareness:** like all MCP tools, the tool schema occupies [[context-window]] space on every turn — see [[token-economy]] for the broader trade-off between tool breadth and context efficiency.

**Don't trust recall for post-cutoff facts.** Anything newer than the model's training cutoff is a [[hallucination]] risk if the model answers from memory — pull it in instead, either via Context7 (version-specific, controlled corpus) or a plain **WebFetch** for one-off facts. Context7 is the right default for fast-moving library APIs; WebFetch for everything else.

<span style="color:red">Context7 is the safer half of the [[knowledge-cutoff]] fix precisely because its source corpus is **vetted** — raw web-fetch can pull in a poisoned page (a malicious Reddit/forum post is the canonical example) and hijack the agent via [[prompt-injection]]. Context7 installs as a harness plugin and can answer questions about a library or language version that post-dates the model's training, on the spot.</span>

## See also
- [[model-context-protocol]]
- [[rag]]
- [[spring-ai]]
- [[context-window]]
- [[token-economy]]
- [[prompt-injection]]
- [[hallucination]]
- [[knowledge-cutoff]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
