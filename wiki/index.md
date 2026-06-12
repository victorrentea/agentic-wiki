---
title: Index
category: source
tags: [index, catalog]
updated: 2026-06-12
---

# Index

Catalog of all wiki pages, grouped by category. Start at [[overview]] for the conceptual map. Read order and provenance live in [[log]].

## Concepts — chat fundamentals
- [[chat-model]] — Spring AI's provider-agnostic `ChatModel`; swap OpenAI ↔ local via a Spring profile.
- [[chat-memory]] — the sliding window of recent messages re-sent on every call.
- [[chat-history]] — the verbatim full transcript stored server-side.
- [[advisor]] — Spring AI interceptor (≈ aspect) that runs before/after the LLM call.
- [[context-window]] — why bigger context isn't better (cost, the dumb zone, lost-in-the-middle).
- [[system-prompt]] — persona + guardrails that never leave the window; what goes into the LLM on turn 1.

## Concepts — MCP & knowledge
- [[model-context-protocol]] — open protocol connecting agents to tools/data; resources, tools, prompts.
- [[mcp-transport]] — SSE + POSTs, JSON-RPC correlation, Streamable HTTP; local stdio transport and its sandbox inheritance.
- [[mcp-sandbox-inheritance]] — spawned (stdio) MCP inherits the agent's OS sandbox; remote MCPs do not.
- [[dynamic-tool-discovery]] — no Swagger: LLM asks server `tools/list` at connect; pitfall when too many tools.
- [[cli-vs-mcp-tradeoff]] — CLI costs ≈0 tokens if in training data; MCP loads tool schemas eagerly (300–600 tokens).
- [[elicitation]] — server forces human-in-the-loop approval mid-tool-call.
- [[sampling]] — a tool calls back into the caller's LLM to condense data.
- [[rag]] — retrieval-augmented generation: pull extra knowledge into the context (≈ approximate find).
- [[vector-search]] — semantic similarity via cosine over embeddings; hybrid RAG.
- [[graph-database]] — explicit-connection knowledge graphs (vs vector similarity).

## Concepts — context & token economy
- [[token-economy]] — treat context tokens as a finite, expensive resource (output ≈5× input).
- [[dumb-zone]] — past ≈65% full a model degrades; 1M window ≠ 1M recall.
- [[prompt-caching]] — pay ≈10% for the re-sent prefix, but the cache TTL is ≈5 minutes.
- [[effort-setting]] — use **xhigh**, never **max**; the reasoning-spend knob.
- [[harness]] — everything wrapped around the model: a UI + tools giving it knowledge and actions.

## Concepts — reliability & human factors
- [[hallucination]] — the five fixable root causes of confident wrongness.
- [[sycophancy]] — the model's trained urge to please; don't insult the AI.
- [[drift]] — the silent divergence of docs/specs/embeddings from the code.
- [[vibe-coding]] — generating code you never read; the opposite of agentic engineering.
- [[agentic-engineering]] — the disciplined practice; autonomy scales with the engineer.
- [[skill-erosion]] — keeping your own competence sharp while AI writes the code.
- [[ai-addiction]] — the dopamine/slot-machine trap of parallel terminals.
- [[caveman]] — anti-pattern: decompressing terse AI output taxes your attention.

## Security
- [[prompt-injection]] — jailbreaks, sycophancy, multi-turn drift, indirect injection via fetched content.
- [[jailbreak-ladder]] — RLHF → abliteration → regex → judge LLM → context accumulation; each rung's bypass.
- [[lethal-trifecta]] — Simon Willison's three-way: private data + untrusted content + external comms = catastrophic.
- [[openclaw]] — always-on personal assistant with payments: the canonical lethal-trifecta product.
- [[geo-steganography]] — Generative Engine Optimization and invisible-text injection into fetched content.
- [[judge-llm]] — a cheap classifier LLM screens prompt and response.
- [[guardrails]] — the layered defense stack, weakest to strongest.
- [[tool-calling]] — the model invents tool parameters; harden and validate them.
- [[tool-context]] — pass identity out-of-band, never as model-authored data.
- [[jwt-identity]] — parse and validate the access token before trusting it.
- [[supply-chain-attack]] — poisoned dependencies, the S-BOM bomb, and the agent-era twist.
- [[dual-leg-rule]] — confidential data XOR external reach; cut one leg.
- [[agent-permissions]] — permission spectrum: default ask → auto-mode → YOLO; tripwires as surgical overrides.
- [[os-sandbox]] — Seatbelt / seccomp+Landlock / bubblewrap: kernel-enforced file+network allow-list.
- [[docker-sandboxing]] — gold standard containment: mount only needed folders, egress allow-list, burn-the-box.
- [[secret-zero]] — MCP-as-proxy / kernel-key-swap broker: agent never holds long-lived credentials.
- [[production-safety]] — least-privilege keys, elicitation gates, kill-all-but-one for autopsy; Railway+Kiro lessons.

## Tools & frameworks
- [[spring-ai]] — the Java framework the chatbot build is based on.
- [[ollama]] — run open LLMs locally (Qwen 2.5) for data residency.
- [[embabel]] — Rod Johnson's JVM agent-workflow framework.
- [[code-graph]] — file-watcher-synced code symbol graph; exact callers + grep-for-reuse.
- [[context7]] — MCP that injects up-to-date, version-specific library docs.
- [[pgvector]] — Postgres vector-similarity extension.
- [[claude-code-router]] — route the harness to other model providers (incl. on-prem DeepSeek).
- [[grill-me]] — a skill that interrogates an idea with probing questions.
- [[llm-wiki]] — Karpathy's pattern that *this* repository instantiates.
- [[rtk]] — Rust Token Killer: proxies dev commands to trim output tokens.
- [[toon]] — Token-Oriented Object Notation: compact CSV-for-AI data format.
- [[wispr-flow]] — voice dictation to remove the human typing bottleneck.
- [[openspec]] — scaffolds and enforces the spec-driven workflow in waves.
- [[worktree]] — isolated repo folders so parallel agents don't collide.
- [[browser-mcp]] — Playwright MCP + accessibility tree: structure-first browser automation.
- [[observability-mcp]] — Grafana / DB / log MCPs; PII risks and the volumetry pattern.
- [[headless-claude]] — `claude -p` / `--resume`: multi-turn user sessions without a custom harness.

## Concepts — skills & knowledge engineering
- [[agent-skill]] — a markdown tool whose description is eager-loaded, body on demand.
- [[progressive-disclosure]] — load knowledge only when relevant; a hook beats discretion.
- [[claude-md]] — project memory; curate it, don't generate it.
- [[tripwire]] — a scripted "kind requirement" that gently nudges the agent.
- [[cross-repo-knowledge]] — the "Dark Horses" central repo + symlinks + `@include`.

## Patterns — operating agents
- [[ralph-loop]] — spec by day, run agent loops overnight with clean context (in a Docker sandbox).
- [[profiling-agents]] — feed JFR / flame graphs / heap dumps to agents; "kill all but one" for post-mortem.
- [[plan-mode]] — single-threaded deep thinking before touching code.
- [[handover]] — write conclusions to a file; "die and be reborn" with clean context.
- [[sub-agents]] — orchestrator delegates to fresh-context research/review minions.
- [[spec-driven-development]] — align your mental model with what the AI will build.
- [[acceptance-test-bdd]] — given/when/then tests you review instead of mock-coupled unit tests.
- [[multi-model-review]] — a "quorum of mothers-in-law"; split each concern across model families; review *sorted*.
- [[cloud-review-workflow]] — "no laptop" pipeline: ticket → Three Amigos → PR bot → cloud SDD → sorted review.
- [[three-amigos]] — Business + Developer + Tester (ninja) session producing acceptance tests.
- [[vibe-fixer]] — receives ≈10k lines of vibe-coded output; applies engineering judgement, not rewriting.
- [[code-review]] — the human's last gate: judgement not mechanics, sorted review, the PR-size sweet spot.
- [[static-analysis-gauntlet]] — Sonar / CodeQL / Semgrep as build-failing CI gates; make the machine draw blood first.
- [[field-reality-diagram]] — sequence diagrams generated from real traces; can't drift.
- [[agent-auth]] — service-account API key + on-behalf-of bearer; identity never as tool argument.

## Sources
- [[2026-06-10-spring-ai-itkonekt]] — Spring AI @ ITKonekt workshop summary (seed source).
- [[2026-06-11-ai-playtika]] — AI Agentic Engineering workshop, day 1 + day 2 (two delta ingests).
