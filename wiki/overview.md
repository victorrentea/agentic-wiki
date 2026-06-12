---
title: Overview — the map of the concepts
category: source
tags: [overview, map, synthesis]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
updated: 2026-06-12
---

# Overview — the map of the concepts

How everything in this wiki connects. The seed source is the [[2026-06-10-spring-ai-itkonekt]] workshop, where a veterinary-clinic chatbot was built live, layer by layer. The concepts cluster into five movements that mirror that build.

## 1. Build a chatbot

Everything starts with [[spring-ai]] and its provider-agnostic [[chat-model]] — the same code runs on remote OpenAI or local [[ollama]]. Because LLM APIs are stateless, the app fakes memory itself: [[chat-memory]] (the sliding window re-sent each call) and [[chat-history]] (the verbatim transcript) are two different beasts, wired in through the [[advisor]] pipeline. You cap the window because of the [[context-window]] economics — bigger isn't better. The [[system-prompt]] sits above the window, defining who the bot is and what it must refuse.

## 2. Secure it

A vanilla bot is trivially broken. [[prompt-injection]] (jailbreaks, sycophancy, multi-turn drift) defeats the polite [[system-prompt]] constraints, so you stack [[guardrails]]: cheap regex first, then a separate [[judge-llm]] that screens both the prompt and the response. The moment the bot gets [[tool-calling]] power, a new hole opens — the model **invents the parameters**, so an attacker can drive it. The fixes are structural: validate the [[jwt-identity]] token, pass sensitive identity via [[tool-context]] (out-of-band, never as model-authored data), and obey the [[dual-leg-rule]] — never give one agent both confidential data *and* external reach. The same paranoia extends to your dependencies: a [[supply-chain-attack]] turns `npm install` into arbitrary code execution, doubly dangerous when an agent runs it on auto-approve.

## 3. Connect over MCP

When the chatbot needs the backend, it speaks [[model-context-protocol]]. The subtle catch: the API key authenticates the *application*, not the human — so identity must ride the [[mcp-transport]] as a header, never as a (forgeable) tool argument. MCP also brings two superpowers REST lacks: [[elicitation]] (force human-in-the-loop approval for hard-to-reverse actions — even Opus 4.8 couldn't cheat past it) and [[sampling]] (a tool borrows your LLM to condense data before flooding your context).

## 4. Give it knowledge

To answer real questions the bot needs knowledge it can **pull** on demand: [[rag]], powered by [[vector-search]] (semantic cosine similarity over embeddings, stored in [[pgvector]]). Vectors are one of two "weird databases"; the other is the [[graph-database]] — explicit connections rather than similarity. [[code-graph]] applies that to source code (exact callers without grep noise), and [[llm-wiki]] applies it to knowledge itself — the pattern *this repository is built on*. [[context7]] is a focused, doc-only flavor of the same "pull fresh knowledge" idea.

## 5. Operate agents at scale

The day's deeper theme: how to run agents responsibly and economically. [[token-economy]] treats context as a scarce resource (tool schemas are expensive; the CLI is often 10× cheaper than an MCP; [[claude-code-router]] swaps the backend provider). The [[ralph-loop]] front-loads a human hour of spec into six hours of overnight agent work, each batch entering with a clean context. [[profiling-agents]] hands flame graphs and heap dumps to the model for wins humans rarely have time for. And [[grill-me]] turns the agent on *your own thinking* before a meeting. The horizon — teed up via [[embabel]] — is structured, multi-stage agentic workflows you can trust in production.

## 6. The agentic-engineering operating discipline

The second source widens "operate agents at scale" into a full craft — [[agentic-engineering]], the deliberate opposite of [[vibe-coding]] (generating code you never read).

**What reaches the model.** Before you type, the [[harness]] has already loaded the [[system-prompt]], your [[claude-md]] memory, every tool schema, and the front-matter of each [[agent-skill]] — visible via `/context`. Token discipline now spans [[prompt-caching]] (a 5-minute TTL trap), [[rtk]] and [[toon]] (compress what the model reads/writes; output is ≈5× input), the [[effort-setting]] (xhigh, never max), and refusing the [[caveman]] anti-pattern so you protect *attention*, not just tokens.

**The dumb zone governs everything.** Past ≈65% full a model degrades ([[dumb-zone]]); a 1M window ≠ recall, and auto-compaction is a failure. You dodge it with a [[handover]] (die and be reborn from a file), [[sub-agents]] (fresh-context minions), or [[plan-mode]] (single-threaded thinking). The same overload causes most [[hallucination]] — alongside [[sycophancy]], vague prompts, missing context, and term confusion.

**Knowledge is engineered, not dumped.** Curate the [[claude-md]] (it [[drift|drifts]]); prefer a [[tripwire]] to a hard rule; load on need via [[progressive-disclosure]]; and share across the studio with [[cross-repo-knowledge]] ("Dark Horses" central repo + symlinks).

**Constrain the chaotic agent from both sides.** [[grill-me]] turns a four-line ticket into a war and ends in a [[handover]]; [[spec-driven-development]] (scaffolded and enforced by [[openspec]]) anticipates every decision; [[acceptance-test-bdd]] tests pin the outside. Before a human looks, the [[static-analysis-gauntlet]] (Sonar / CodeQL / Semgrep as build-failing CI gates) makes the code self-repair; then review with humans + a [[multi-model-review]] quorum — sorted, judgement-only [[code-review]] where attention is the scarce resource — run agents safely in [[worktree|worktrees]] and a [[supply-chain-attack]]-hardened Docker sandbox, and keep diagrams honest with a [[field-reality-diagram]]. Throughout, guard the human against [[ai-addiction]] and [[skill-erosion]].

## 7. <span style="color:red">Secure and contain your agents (Day 2)</span>

<span style="color:red">Day 2 expanded the security cluster from "how to break a chatbot" to "how to contain an agent at production scale."

**The attack surface.** The [[jailbreak-ladder]] maps the escalating bypass for each [[guardrails|guardrail]] layer (RLHF → abliteration → regex → [[judge-llm]] → context accumulation); none are guarantees. Deeper: [[prompt-injection]] now includes fetched web pages, emails, and screenshots as vectors — see [[geo-steganography]] for invisible-text and GEO attacks. The architectural-level risk has a name: Simon Willison's [[lethal-trifecta]] (private data + untrusted content + external comms). [[openclaw]] is the canonical product that instantiates all three legs. The only structural fix is removing a leg — not filtering harder.

**MCP under the hood.** [[mcp-transport]] now has two distinct shapes: remote SSE/HTTP (needs its own authz — outside the agent's sandbox) vs local stdio (spawned child → [[mcp-sandbox-inheritance]] — the sandbox applies automatically). [[dynamic-tool-discovery]] is the mechanism (no Swagger needed), but too many tools confuse the model; [[cli-vs-mcp-tradeoff]] quantifies the token cost difference. [[browser-mcp]] (Playwright + accessibility tree) lets agents drive browsers structurally rather than via costly screenshots. [[observability-mcp]] brings Grafana, DB, and log MCPs — with a hard PII access-review requirement. [[agent-auth]] gives the dual-credential model (service-account + on-behalf-of bearer).

**Containment ladder.** [[agent-permissions]] maps the spectrum (ask → auto-mode → YOLO) and explains why denying part of bash is naïve. [[os-sandbox]] (Seatbelt / seccomp+Landlock / bubblewrap) is kernel-enforced; [[docker-sandboxing]] is the gold standard (mount only needed folders, egress allow-list, never mount the socket, burn-the-box). [[secret-zero]] ensures agents never hold long-lived credentials — the MCP-as-proxy and kernel-key-swap broker patterns. [[production-safety]] consolidates the lessons from two documented disasters (Railway: 3 months of data gone; Kiro: 13-hour AWS outage) into actionable posture: least-privilege keys, [[elicitation]] on destructive actions, and "kill all but one" for post-mortem.

**The cloud review workflow.** [[cloud-review-workflow]] is the "no laptop" pipeline: business vibes UX → [[vibe-fixer]] cleans it → [[three-amigos]] (Business + Developer + Tester ninja) produce acceptance tests → PR auto-created → cloud bot researches code → SDD → review by role → tasks for a cheaper model. The [[headless-claude]] (`claude -p` + `--resume`) pattern lets teams fake multi-turn user interactions without building a harness.</span>

---

*This page is the synthesis layer. Browse it in [Obsidian](https://obsidian.md/) graph view to see the clusters as a literal map.*
