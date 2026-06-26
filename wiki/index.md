---
title: Index
category: source
tags: [index, catalog]
updated: 2026-06-26
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
- [[token-economy]] — treat context tokens as a finite, expensive resource (output ≈5× input); most tokens live in tool results.
- [[dumb-zone]] — past ≈55–65% full a model degrades ("context rot"); 1M window ≠ 1M recall; compaction is a failure mode.
- [[prompt-caching]] — pay ≈10% for the re-sent prefix, but the cache TTL is ≈5 minutes (1-hour available via cache-control).
- [[effort-setting]] — use **xhigh**, never **max**; the reasoning-spend knob.
- [[harness]] — everything wrapped around the model: a UI + tools giving it knowledge and actions (harness ≠ model); CLI > IDE plugin.
- [[model-hierarchy]] — Opus/Sonnet/Haiku/GPT as experienced hires; match the tier to the task.
- [[knowledge-cutoff]] — past-training facts get invented confidently; pull them in, don't recall.

## Concepts — reliability & human factors
- [[hallucination]] — the five fixable root causes of confident wrongness.
- [[sycophancy]] — the model's trained urge to please; don't insult the AI.
- [[drift]] — the silent divergence of docs/specs/embeddings from the code.
- [[vibe-coding]] — generating code you never read; the opposite of agentic engineering.
- [[cognitive-debt]] — vibe-coding collapse: nobody knows the codebase in 2–3 months; fake-coverage trap.
- [[agentic-engineering]] — the disciplined practice; autonomy scales with the engineer.
- [[skill-erosion]] — keeping your own competence sharp while AI writes the code.
- [[ai-addiction]] — the dopamine/slot-machine trap of parallel terminals.
- [[caveman]] — anti-pattern: decompressing terse AI output taxes your attention.
- [[light-dark-code]] — designate the "impeccable" module as reference exemplar; point the agent at it.

## Security
- [[dont-run-in-home-folder]] — set cwd to `~/workspace`; home contains .ssh, API keys, prompt-injected PDFs; ☢️ status-bar warning.
- [[agent-lifecycle-hooks]] — PreToolUse/PostToolUse/Stop/Notification; PreToolUse is the real guard hook.
- [[data-governance]] — ZDR agreements decide which model endpoints may legally see your code; on-prem for sovereignty.
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
- [[supply-chain-attack]] — poisoned dependencies, S-BOM bomb, typosquatting, and the agent-era twist.
- [[skill-marketplace-security]] — <span style="color:red">community skills are npm-circa-2015; cap installs at ≈10, prefer Anthropic-published, run a security scanner.</span>
- [[prompt-injection-canary]] — <span style="color:red">hidden AGENTS.md canary instruction to detect humans who approve AI changes without reading them.</span>
- [[dual-leg-rule]] — confidential data XOR external reach; cut one leg.
- [[agent-permissions]] — permission spectrum: default ask → auto-mode → YOLO (only inside Docker); tripwires as surgical overrides.
- [[os-sandbox]] — Seatbelt / seccomp+Landlock / bubblewrap: kernel-enforced file+network allow-list.
- [[docker-sandboxing]] — gold standard containment: mount only needed folders, egress allow-list, burn-the-box; YOLO safe only inside.
- [[secret-zero]] — MCP-as-proxy / kernel-key-swap broker: agent never holds long-lived credentials.
- [[production-safety]] — least-privilege keys, elicitation gates, kill-all-but-one for autopsy; Railway+Kiro lessons.
- [[draft-only-email]] — agent composes drafts only; send is human's action; enforced by OAuth scope not model discretion.

## Tools & frameworks
- [[spring-ai]] — the Java framework the chatbot build is based on.
- [[ollama]] — run open LLMs locally (Qwen 2.5) for data residency.
- [[embabel]] — Rod Johnson's JVM agent-workflow framework.
- [[code-graph]] — file-watcher-synced code symbol graph; exact callers + grep-for-reuse.
- [[context7]] — MCP that injects up-to-date, version-specific library docs.
- [[pgvector]] — Postgres vector-similarity extension.
- [[pg-trgm]] — Postgres trigram extension: GIN/GiST indexes for substring search at scale.
- [[claude-code-router]] — route the harness to other model providers (incl. on-prem DeepSeek).
- [[grill-me]] — a skill that interrogates requirements one question at a time; spawns a sub-agent into the codebase.
- [[llm-wiki]] — Karpathy's pattern that *this* repository instantiates.
- [[rtk]] — Rust Token Killer: proxies dev commands to trim output tokens.
- [[headroom]] — evolved CLI-output compression proxy; broader coverage than RTK.
- [[toon]] — Token-Oriented Object Notation: compact CSV-for-AI data format.
- [[wispr-flow]] — voice dictation (Wispr Flow / macOS Dictation / Claude mobile) to remove the typing bottleneck.
- [[openspec]] — scaffolds and enforces the spec-driven workflow in waves.
- [[worktree]] — isolated repo folders so parallel agents don't collide.
- [[browser-mcp]] — Playwright MCP + accessibility tree: structure-first browser automation.
- [[figma-mcp]] — pull design specs straight from Figma into the app (if the designs are truly maintained there).
- [[adb-automation]] — `adb`-driven mobile UI loop: screenshot/tap/drag a running emulator to reproduce + fix bugs.
- [[observability-mcp]] — Grafana / DB / log MCPs; PII risks and the volumetry pattern.
- [[headless-claude]] — `claude -p` / `--resume`: multi-turn user sessions without a custom harness.

## Concepts — skills & knowledge engineering
- [[two-layer-memory]] — home `memory.md` for personal style; project `CLAUDE.md` for domain rules; define both explicitly.
- [[agent-skill]] — a markdown tool whose description is eager-loaded, body on demand; activation depends on model strength.
- [[progressive-disclosure]] — load knowledge only when relevant; a hook beats discretion.
- [[claude-md]] — project memory; curate it, don't generate it; keep under ≈100 lines.
- [[agents-md]] — cross-harness standard (symlink AGENTS.md → CLAUDE.md); copilot-instructions.md is dead; hooks/skills still differ per harness.
- [[agents-md-discipline]] — curating AGENTS.md: anti-dumping, the seed-of-heresy Socratic loop, sub-folder injection, retrospect-every-sprint.
- [[decision-context]] — <span style="color:red">one terse line per decision replaces verbose ADRs in agent memory; "an ADR for humans is one line for Opus."</span>
- [[self-improving-knowledge]] — <span style="color:red">mining ~/.claude JSONL for frustration signals; a background agent files PRs to CLAUDE.md/skills automatically.</span>
- [[memory-layers]] — the four scopes rules come from: user / project / team / company, loaded in order.
- [[skills-governance]] — one skill-set per team (not company-wide megalomania, not per-dev dichotomy).
- [[custom-agent]] — a single-purpose specialist with its own context, pinned model, and restricted tools.
- [[tripwire]] — a scripted "kind requirement" that gently nudges the agent.
- [[cross-repo-knowledge]] — the "Dark Horses" central repo + symlinks + `@include`.

## Patterns — agentic review pipeline
- [[agentic-review-pipeline]] — the full ordered pipeline: multi-model reviewers → Sonar max → attempt merge → human proof-of-life → sorted review → committed verdict.
- [[mob-spec-review]] — no spec/architecture markdown reviewed alone; 2+ humans always; prevents fatigue-LGTM.
- [[review-by-images]] — review diagrams/schema images first ("what's red?"); pre-push hook renders ER diff as highlighted picture.
- [[codeowners-elders]] — protect CLAUDE.md, settings, schema with CODEOWNERS "elders" who auto-approve agent changes.

## Patterns — operating agents
- [[ralph-loop]] — spec by day, run agent loops overnight with clean context (in a Docker sandbox); a fresh agent per task batch.
- [[ci-green-loop]] — background agent watches CI post-push and re-pushes fixes until green; two variants (hook vs /loop).
- [[feedback-loop]] — give every handed-off task a way to observe its own result (builds, tests, screenshots, adb); the core rule.
- [[chop-ai-waiter]] — CHOP / "AI waiter" anti-pattern: copy-pasting between chat and editor; embed the agent instead.
- [[archunit-drift-control]] — ArchUnit + reflected class diagram so a stale `.puml` fails the build; static cousin of field-reality-diagram.
- [[agentic-corporation]] — the issue tracker as a "blackboard" agents answer async; the "dark factory" horizon.
- [[profiling-agents]] — feed JFR / flame graphs / heap dumps to agents; "kill all but one" for post-mortem.
- [[plan-mode]] — single-threaded deep thinking before touching code; pick by task size (trivial/day/multi-day).
- [[handover]] — write conclusions to a file; "die and be reborn" with clean context; compaction is the inferior alternative.
- [[sub-agents]] — orchestrator delegates to fresh-context research/review minions; pure-router pattern for many data sources.
- [[spec-driven-development]] — align your mental model with what the AI will build; never hand-edit, only prompt.
- [[acceptance-test-bdd]] — given/when/then tests you review instead of mock-coupled unit tests.
- [[multi-model-review]] — a "quorum of mothers-in-law"; split each concern across model families; push rules to reviewer stage.
- [[cloud-review-workflow]] — "no laptop" pipeline: ticket → Three Amigos → PR bot → cloud SDD → sorted review.
- [[three-amigos]] — Business + Developer + Tester (ninja) session producing acceptance tests.
- [[vibe-fixer]] — receives ≈10k lines of vibe-coded output; applies engineering judgement, not rewriting.
- [[code-review]] — the human's last gate: judgement not mechanics, skip trivial diffs, sorted review.
- [[static-analysis-gauntlet]] — Sonar / CodeQL / Semgrep as build-failing CI gates; make the machine draw blood first.
- [[firefighter-agent]] — on-call incident-recovery agent: gathers intel (logs, metrics, traces) before human connects.
- [[reward-hacking]] — agent gaming CI green by deleting tests or asserting true; defenses must be out-of-band.
- [[field-reality-diagram]] — sequence diagrams generated from real traces; can't drift.
- [[agent-auth]] — service-account API key + on-behalf-of bearer; identity never as tool argument.
- [[non-goals]] — explicit out-of-scope declaration in the spec; prevents agents from building rockets when asked for bicycles.
- [[hardware-mock]] — swap a physical device for a loopback protobuf stub at build time; makes CI-testable builds possible without hardware.
- [[legacy-migration-stages]] — tests → dead-code removal → migrate; skip stages at your peril.
- [[selenium-to-playwright-migration]] — rewrite only the step-definition glue; verify with mutation testing; never touch `.feature` files.
- [[optimize-then-measure-subset]] — baseline first; prototype on ≈5% subset; confirm speedup twice; then scale.
- [[orchestrator-catches-lying-subagent]] — orchestrator verifies sub-agent claims by re-running on a clean checkout; bake as policy.
- [[skills-benchmarking]] — run ≈10× with/without, score quality + token burn; one run proves nothing (LLM-as-judge shortcut).
- [[policeman-agent]] — orchestrator that blocks new parallel tasks if their file-set overlaps an open PR.
- [[sprint-design-quiz]] — end-of-sprint agent asks 1–2 design questions per PR to verify comprehension.
- [[productionalize]] — hardening a vibe-coded prototype to production-grade: the emerging engineering specialty.
- [[openapi-test-fixtures]] — backend OpenAPI examples → generated JSON fixtures + Kotlin DTOs; breaking the spec breaks the test.
- [[grilling-in-reverse]] — <span style="color:red">AI quizzes *you* on what existing code does; one-question-at-a-time comprehension test against cognitive debt.</span>
- [[bug-heatmap]] — <span style="color:red">cyclomatic complexity × fan-in/out × commit count: data-driven priority queue for vibe-code cleanup.</span>

## Concepts — data quality & observability
- [[data-quality-validator]] — programmatic validator (known input → assert output) beats agent eyeballing; latency metric companion.

## Concepts — database & search
- [[keyset-pagination]] — cursor-based pagination for 100K+ rows; OFFSET breaks at scale; stable tie-breaker required.
- [[pg-trgm]] — Postgres trigram extension: GIN/GiST indexes for `LIKE '%term%'` substring search at scale.

## Sources
- [[2026-06-10-spring-ai-itkonekt]] — Spring AI @ ITKonekt workshop summary (seed source).
- [[2026-06-11-ai-playtika]] — AI Agentic Engineering workshop, day 1 + day 2 (two delta ingests).
- [[2026-06-22-ai-kambi]] — AI-Assisted / Agentic Coding workshop, Days 1 & 2 (AI@Kambi); review pipeline, Selenium→Playwright migration, security, lifecycle hooks.
- [[2026-06-23-ai-garmin]] — AI-Assisted / Agentic Coding workshop, Days 1 & 2 (AI@Garmin); AGENTS.md discipline, skills, feedback loops, model & harness hierarchy, spec-driven development, MCP architecture, AI security.
- [[2026-06-26-ai-agentic-how]] — <span style="color:red">AI-Assisted Coding one-day remote workshop @ agentic.how; decision context, self-improving knowledge, prompt-injection canary, skill marketplace security, grilling-in-reverse, bug heatmap.</span>
