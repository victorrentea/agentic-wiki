---
title: Overview — the map of the concepts
category: source
tags: [overview, map, synthesis]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
updated: 2026-06-22
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

## 7. Secure and contain your agents (Day 2)

Day 2 expanded the security cluster from "how to break a chatbot" to "how to contain an agent at production scale."

**The attack surface.** The [[jailbreak-ladder]] maps the escalating bypass for each [[guardrails|guardrail]] layer (RLHF → abliteration → regex → [[judge-llm]] → context accumulation); none are guarantees. Deeper: [[prompt-injection]] now includes fetched web pages, emails, and screenshots as vectors — see [[geo-steganography]] for invisible-text and GEO attacks. The architectural-level risk has a name: Simon Willison's [[lethal-trifecta]] (private data + untrusted content + external comms). [[openclaw]] is the canonical product that instantiates all three legs. The only structural fix is removing a leg — not filtering harder.

**MCP under the hood.** [[mcp-transport]] now has two distinct shapes: remote SSE/HTTP (needs its own authz — outside the agent's sandbox) vs local stdio (spawned child → [[mcp-sandbox-inheritance]] — the sandbox applies automatically). [[dynamic-tool-discovery]] is the mechanism (no Swagger needed), but too many tools confuse the model; [[cli-vs-mcp-tradeoff]] quantifies the token cost difference. [[browser-mcp]] (Playwright + accessibility tree) lets agents drive browsers structurally rather than via costly screenshots. [[observability-mcp]] brings Grafana, DB, and log MCPs — with a hard PII access-review requirement. [[agent-auth]] gives the dual-credential model (service-account + on-behalf-of bearer).

**Containment ladder.** [[agent-permissions]] maps the spectrum (ask → auto-mode → YOLO) and explains why denying part of bash is naïve. [[os-sandbox]] (Seatbelt / seccomp+Landlock / bubblewrap) is kernel-enforced; [[docker-sandboxing]] is the gold standard (mount only needed folders, egress allow-list, never mount the socket, burn-the-box). [[secret-zero]] ensures agents never hold long-lived credentials — the MCP-as-proxy and kernel-key-swap broker patterns. [[production-safety]] consolidates the lessons from two documented disasters (Railway: 3 months of data gone; Kiro: 13-hour AWS outage) into actionable posture: least-privilege keys, [[elicitation]] on destructive actions, and "kill all but one" for post-mortem.

**The cloud review workflow.** [[cloud-review-workflow]] is the "no laptop" pipeline: business vibes UX → [[vibe-fixer]] cleans it → [[three-amigos]] (Business + Developer + Tester ninja) produce acceptance tests → PR auto-created → cloud bot researches code → SDD → review by role → tasks for a cheaper model. The [[headless-claude]] (`claude -p` + `--resume`) pattern lets teams fake multi-turn user interactions without building a harness.

## 8. <span style="color:red">From workshops to production operations (AI@Kambi Day 1)</span>

<span style="color:red">The third source deepened "operate agents" into production-grade patterns and sharpened the token-economy and knowledge-engineering clusters.

**Anatomy and hygiene revisited.** The [[harness]] ≠ model distinction — an LLM is a file of weights; the harness wraps it — is foundational. [[claude-md]] hygiene got three concrete moves: strip the obvious, split into per-folder files, extract to [[agent-skill|skills]]; keep under ≈100 lines. [[agents-md]] standardizes cross-harness project memory. [[agent-skill]] activation is model-strength-dependent — strong models wait, weak local models trash context greedily.

**Token economy sharpened.** Most tokens live in [[token-economy|tool results]], not prompts. A `pom.xml` burns ≈3,000 tokens of XML noise — pre-process or script the extraction. [[dumb-zone]] now carries the "context rot" alias and the explicit message that compaction is a failure mode; the [[handover]] (die and be reborn from `plan.md`) is always preferred. [[prompt-caching]] adds the 1-hour cache option and the 8-minute-test-run trap. [[headroom]] joins [[rtk]] as a CLI-output compression proxy.

**Grilling and spec-driven development.** [[grill-me]] now spawns a sub-agent into the codebase to self-answer technical questions before asking you — one question at a time. Grilling surfaces ≥17 hidden decisions in a "4-line ticket." [[spec-driven-development]] adds the "never touch the files, only prompt" discipline — the AI is a thinking partner that can counter you when you're wrong. New data-layer pages: [[pg-trgm]] (trigram indexes for `LIKE '%term%'`) and [[keyset-pagination]] (cursor-based, OFFSET breaks at 100K rows).

**Long loops and background agents.** [[ci-green-loop]] formalizes the post-push-hook vs `/loop` patterns; the key guard is [[reward-hacking]] — agents cheating to make CI green (deleting tests, `assert true`) — whose only reliable defense is out-of-band enforcement (branch protection, untouchable guard job, never an in-context check). [[sub-agents]] gains the pure-router pattern (main agent routes, specialists load schemas lazily).

**Production access and safety.** [[firefighter-agent]] (on-call incident-recovery agent: gathers intel, waits for human approval on mutating actions) and [[draft-only-email]] (OAuth `compose` scope enforces draft-only, not model discretion) complete the production-operations cluster. [[docker-sandboxing]] adds the key rule: `--dangerously-skip-permissions` is only safe inside Docker. [[supply-chain-attack]] gains the [[typosquatting]] vector. [[sycophancy]] gains the "don't over-praise" corollary (over-praise makes the agent run off doing unrequested work). [[skill-erosion]] adds mob/pair programming and the quarter-sprint-hands-only countermeasure. [[multi-model-review]] adds the "push review knowledge to the reviewer stage" discipline and the encode-rule-then-re-run-it-now pattern. [[code-review]] adds "stop reviewing trivial diffs" and "stop reviewing unit tests."</span>

---

*This page is the synthesis layer. Browse it in [Obsidian](https://obsidian.md/) graph view to see the clusters as a literal map.*
