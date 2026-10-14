---
title: Index
category: source
tags: [index, catalog]
updated: 2026-10-13
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
- [[token-economy]] — treat context tokens as a finite, expensive resource; most tokens live in tool results <span style="color:red">(command output is billed as *input*); compression tools measured at ≈0–10%, not 30%</span>.
- [[dumb-zone]] — <span style="color:red">quality degrades well before the window is full (sessions observe ≈65–85%); 1M window ≠ 1M reasoning — single-fact recall holds, multi-fact reasoning drops; auto-compaction fires near the limit</span>.
- [[prompt-caching]] — pay ≈10% for the re-sent prefix; <span style="color:red">API default TTL 5 min, 1 h opt-in at 2× write; Claude Code subscriptions get 1 h on the main thread, sub-agents/API keys 5 min</span>.
- [[effort-setting]] — <span style="color:red">low → max; default high (medium on 5.5 models); raise to xhigh for planning or cross-boundary work; max overthinks</span>.
- [[harness]] — everything wrapped around the model: a UI + tools giving it knowledge and actions (harness ≠ model); CLI > IDE plugin.
- [[model-hierarchy]] — Opus/Sonnet/Haiku/GPT as experienced hires; match the tier to the task.
- [[knowledge-cutoff]] — past-training facts get invented confidently; pull them in, don't recall.
- [[quota-economics]] — 🆕 <span style="color:red">Money side of agentic coding: subscription vs API vs Copilot credits, quota negotiation, estimating tasks in hours and dollars.</span>
- [[agent-loop]] — 🆕 <span style="color:red">Every agent is the same loop: call model with tools, execute tool calls, append results, repeat; where state, cost and control live.</span>

## Concepts — reliability & human factors
- [[hallucination]] — the five fixable root causes of confident wrongness.
- [[sycophancy]] — the model's trained urge to please; don't insult the AI.
- [[drift]] — the silent divergence of docs/specs/embeddings from the code.
- [[vibe-coding]] — generating code you never read; the opposite of agentic engineering.
- [[cognitive-debt]] — vibe-coding collapse: nobody knows the codebase in 2–3 months; fake-coverage trap.
- [[agentic-engineering]] — the disciplined practice; autonomy scales with the engineer.
- [[skill-erosion]] — keeping your own competence sharp while AI writes the code.
- [[ai-addiction]] — the dopamine/slot-machine trap of parallel terminals.
- [[caveman]] — <span style="color:red">a skill that makes the model answer telegraphically to save output tokens — measured ≈8.5%, and it shifts the decoding cost onto you</span>.
- [[light-dark-code]] — designate the "impeccable" module as reference exemplar; point the agent at it.
- [[ai-spiraling]] — 🆕 <span style="color:red">Agent loops on a wrong path for 10–30 min; stop after ≈3 failed tries, ask "how did you get here?", /clear with the missing knowledge.</span>
- [[ai-never-deletes]] — 🆕 <span style="color:red">Agents add code beside old code instead of replacing it; dead methods, overloads, duplication — and how to counter it.</span>
- [[vibe-heresy]] — 🆕 <span style="color:red">Bad AI-written decisions that linger in comments, tests and markdown and resurrect in later prompts; mechanism and clean-up defences.</span>
- [[system-2-fatigue]] — 🆕 <span style="color:red">AI took over coding's restful System-1 stretches and left all-day deep thinking: fatigue-LGTM, compression ratios, brain budgets.</span>
- [[answer-injection]] — 🆕 <span style="color:red">Biasing an agent by smuggling the answer into the prompt; describe the symptom, ask neutral questions, steer persona not solution.</span>
- [[ai-calisthenics]] — 🆕 <span style="color:red">Ladder of uncomfortable self-imposed drills (problem not order, no hand edits, zero paste, dictation, 4 parallel agents) for agentic reconversion.</span>
- [[agentic-reconversion]] — 🆕 <span style="color:red">Retraining developers for agent-written code: career directions (product, architecture, quality, FDE) and the mindsets that now earn the salary.</span>
- [[ai-adoption]] — 🆕 <span style="color:red">How engineering orgs actually adopt agentic development: bottom-up, laggers found by token data, skeptics converted by pairing, paved roads.</span>
- [[advisor-vs-executor]] — 🆕 <span style="color:red">Two ways to use AI: advisor (it proposes, you judge; roll the dice on several models) vs executor (it proves its work against a measurable signal).</span>
- [[approval-fatigue]] — 🆕 <span style="color:red">Humans approve 93–97% of permission prompts and stop reading; sourced evidence, room heuristics flagged as such, and remedies (classifier, keys, sandbox).</span>
- [[goals-over-instructions]] — 🆕 <span style="color:red">Write prompts and skills as objectives and constraints, not step-by-step procedures; let the stronger model choose the how.</span>
- [[example-driven-prompting]] — 🆕 <span style="color:red">Show the agent concrete examples (few-shot cases, example files, exemplar modules) instead of prose; polish one slice, then "do the rest".</span>

## Security
- [[dont-run-in-home-folder]] — set cwd to `~/workspace`; home contains .ssh, API keys, prompt-injected PDFs; ☢️ status-bar warning.
- [[agent-lifecycle-hooks]] — PreToolUse/PostToolUse/Stop/Notification; PreToolUse is the real guard hook <span style="color:red">— but a tripwire, not a wall (alternate spellings, timeouts, exit 1)</span>.
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
- [[supply-chain-attack]] — poisoned dependencies, S-BOM bomb, typosquatting, and the agent-era twist; <span style="color:red">chalk/debug, Shai-Hulud and axios as three separate incidents</span>.
- [[skill-marketplace-security]] — community skills are npm-circa-2015; <span style="color:red">Snyk: 36.8% flawed, 13.4% critical, 76 malicious of 3,984</span>; cap installs, prefer Anthropic-published, run a security scanner.
- [[prompt-injection-canary]] — hidden AGENTS.md canary instruction to detect humans who approve AI changes without reading them.
- [[dual-leg-rule]] — confidential data XOR external reach; cut one leg.
- [[agent-permissions]] — permission spectrum: default ask → auto-mode → YOLO (only inside Docker); tripwires as surgical overrides.
- [[os-sandbox]] — Seatbelt / seccomp+Landlock / bubblewrap: kernel-enforced file+network allow-list.
- [[docker-sandboxing]] — gold standard containment: mount only needed folders, egress allow-list, burn-the-box; YOLO safe only inside.
- [[secret-zero]] — MCP-as-proxy / kernel-key-swap broker: agent never holds long-lived credentials.
- [[production-safety]] — least-privilege keys, elicitation gates, kill-all-but-one for autopsy; Railway+Kiro lessons <span style="color:red">(Railway: raw API call with a CLI token; Kiro: disputed, no database dropped)</span>.
- [[draft-only-email]] — agent composes drafts only; send is human's action; enforced by OAuth scope not model discretion.
- [[memory-poisoning]] — 🆕 <span style="color:red">Prompt injection that persists in MEMORY.md/CLAUDE.md/config so it re-runs every session; attack shapes (incl. jqwik protestware) and defences.</span>
- [[slopsquatting]] — 🆕 <span style="color:red">Attackers pre-register package names LLMs hallucinate (≈5.2% commercial / ≈21.7% open models); how agents amplify it and the defences.</span>
- [[tool-poisoning]] — 🆕 <span style="color:red">Prompt injection through MCP tool and skill descriptions loaded at start-up, rug-pull updates, and the pinning and vetting defences.</span>
- [[gitleaks]] — 🆕 <span style="color:red">Pre-commit secret scanner as a fence the agent can't argue past; its blind spots, agents routing around it, CI backstop and prompt-side twin.</span>
- [[mcp-tenant-isolation]] — 🆕 <span style="color:red">The MCP server injects the tenant filter into every agent SQL query (or DB RLS) — security by tooling, not prompting.</span>
- [[enforcement-ladder]] — 🆕 <span style="color:red">Escalation from asking the agent nicely to hooks, CI gates, sandboxes and credentials; why a regex deny is only a tripwire.</span>
- [[authenticated-cli]] — 🆕 <span style="color:red">Letting the agent call CLIs you're already logged into (az, gh, psql): no MCP, no token in context, your blast radius.</span>
- [[uncensored-models]] — 🆕 <span style="color:red">Abliterated open-weight models: how refusal is removed, red-teaming your sandbox, open weights ≠ open source.</span>

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
- [[rtk]] — Rust Token Killer: proxies dev commands to trim output tokens; <span style="color:red">JetBrains measured +7.6% cost at low effort, ±0 at high</span>.
- [[headroom]] — evolved CLI-output compression proxy; <span style="color:red">a PointFive study measured +48% billed cost</span>.
- [[toon]] — Token-Oriented Object Notation: compact CSV-for-AI data format.
- [[wispr-flow]] — voice dictation (Wispr Flow / macOS Dictation / Claude mobile) to remove the typing bottleneck.
- [[openspec]] — scaffolds and enforces the spec-driven workflow in waves.
- [[worktree]] — isolated repo folders so parallel agents don't collide.
- [[browser-mcp]] — Playwright MCP + accessibility tree: structure-first browser automation.
- [[figma-mcp]] — pull design specs straight from Figma into the app (if the designs are truly maintained there).
- [[adb-automation]] — `adb`-driven mobile UI loop: screenshot/tap/drag a running emulator to reproduce + fix bugs.
- [[observability-mcp]] — Grafana / DB / log MCPs; PII risks and the volumetry pattern.
- [[headless-claude]] — `claude -p` / `--resume`: multi-turn user sessions without a custom harness.
- [[kiro]] — 🆕 <span style="color:red">AWS's spec-driven agentic IDE/CLI: EARS requirements, steering inclusion modes, skills-as-powers, hooks, and the disputed Dec-2025 outage report.</span>
- [[superpowers]] — 🆕 <span style="color:red">obra's community skill pack: brainstorming, systematic debugging, TDD, sub-agent review; when to use vs OpenSpec, costs and caveats.</span>
- [[openrewrite]] — 🆕 <span style="color:red">AST refactoring engine: let the agent write and test recipes from one worked example instead of mass-editing code by prompt.</span>
- [[code-city]] — 🆕 <span style="color:red">Victor's 3D codebase city (after Wettel's CodeCity): size/complexity/heat per class, used to diff a PR's structure and flag hotspots for agents.</span>
- [[plugin-marketplace]] — 🆕 <span style="color:red">A git repo of plugins (skills, hooks, MCPs) auto-pulled by the team's agents: per-team, feedback skill first, never strangers'.</span>

## Concepts — skills & knowledge engineering
- [[two-layer-memory]] — home `memory.md` for personal style; project `CLAUDE.md` for domain rules; define both explicitly.
- [[agent-skill]] — a markdown tool whose description is eager-loaded, body on demand; activation depends on model strength.
- [[progressive-disclosure]] — load knowledge only when relevant; a hook beats discretion; <span style="color:red">`@`-imports load eagerly — they are not progressive</span>.
- [[claude-md]] — project memory; curate it, don't generate it; keep it short <span style="color:red">(ETH Zürich: context files cost >20% more without raising success)</span>.
- [[agents-md]] — cross-harness standard; <span style="color:red">Claude Code reads it natively since v2.1.277 (only without a CLAUDE.md) — bridge with `@AGENTS.md` inside CLAUDE.md, not a symlink; Copilot still reads copilot-instructions.md</span>; hooks/skills still differ per harness.
- [[agents-md-discipline]] — curating AGENTS.md: anti-dumping, the seed-of-heresy Socratic loop, sub-folder injection, retrospect-every-sprint.
- [[decision-context]] — one terse line per decision replaces verbose ADRs in agent memory; "an ADR for humans is one line for Opus."
- [[self-improving-knowledge]] — mining ~/.claude JSONL for frustration signals; a background agent files PRs to CLAUDE.md/skills automatically.
- [[memory-layers]] — the four scopes rules come from: user / project / team / company — <span style="color:red">concatenated, never overridden; sub-folder files load lazily</span>.
- [[skills-governance]] — one skill-set per team (not company-wide megalomania, not per-dev dichotomy).
- [[custom-agent]] — a single-purpose specialist with its own context, pinned model, and restricted tools.
- [[tripwire]] — a scripted "kind requirement" that gently nudges the agent.
- [[cross-repo-knowledge]] — the central team repo + symlinks + `@include`.
- [[doc-gardener]] — 🆕 <span style="color:red">Scheduled agent that prunes and resyncs CLAUDE.md/AGENTS.md, skills and docs against the code to fight drift and bloat.</span>
- [[elder-wisdom-mining]] — 🆕 <span style="color:red">Agent-run interviews and footprint mining of departing experts, distilled into team memory so undocumented decisions don't leave with them.</span>
- [[internal-tool-cli-skill]] — 🆕 <span style="color:red">Reverse-engineer an in-house tool (sources, decompile, Playwright capture), ship a token-efficient CLI plus SKILL.md, secrets in .env.</span>
- [[deterministic-diagrams]] — 🆕 <span style="color:red">Feed agents only script-generated or test-verified diagrams (OpenAPI, DDL, PlantUML, C4, hand-laid draw.io checked by tests).</span>
- [[c4-model]] — 🆕 <span style="color:red">C4 levels for agent-built systems: generate C2–C4 from code/traces (Structurizr, PlantUML), enforce with ArchUnit, filtered views for review.</span>
- [[observation-points]] — 🆕 <span style="color:red">Own the solution, not the code: deterministically generated views (diagrams, contracts, code city) whose diffs the engineer watches.</span>
- [[determinism-over-llm]] — 🆕 <span style="color:red">If you can script it, script it: leave scripts, codemods and linters behind; reserve the LLM for judgement.</span>

## Patterns — agentic review pipeline
- [[agentic-review-pipeline]] — the full ordered pipeline: multi-model reviewers → Sonar max → attempt merge → human proof-of-life → sorted review → committed verdict.
- [[mob-spec-review]] — no spec/architecture markdown reviewed alone; 2+ humans always; prevents fatigue-LGTM.
- [[review-by-images]] — review diagrams/schema images first ("what's red?"); pre-push hook renders ER diff as highlighted picture.
- [[codeowners-elders]] — protect CLAUDE.md, settings, schema with CODEOWNERS "elders" who auto-approve agent changes.
- [[gray-factory]] — 🆕 <span style="color:red">Dimming human review responsibly: sampling rule (10 good PRs → half), preconditions, upgrade loop, factory culture.</span>
- [[assumptions-ledger]] — 🆕 <span style="color:red">Coding agent logs assumptions with confidence plus rejected reviewer findings, so human review starts where the agent hesitated.</span>
- [[human-review-dashboard]] — 🆕 <span style="color:red">Script-generated, zero-token HTML review page per PR (diagrams, oasdiff, schema diff, demo video, requirement coverage); AI enters last.</span>
- [[semantic-coverage]] — 🆕 <span style="color:red">Map tests through a PR's changed code to the spec sentences they verify; executed-but-unasserted shown as partial.</span>
- [[stacked-prs]] — 🆕 <span style="color:red">Splitting big agent diffs into chained small PRs within review budget; native GitHub stacks, cascading fixes, dissent.</span>
- [[spec-review]] — 🆕 <span style="color:red">How to review agent specs: read every word, sleep on it, per-file audience, red flags, size caps, three-amigos meeting.</span>
- [[openapi-contract-sync]] — 🆕 <span style="color:red">Derive OpenAPI from backend code, regenerate frontend types, fail the build/push on any contract drift.</span>

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
- [[grilling-in-reverse]] — AI quizzes *you* on what existing code does; one-question-at-a-time comprehension test against cognitive debt.
- [[bug-heatmap]] — cyclomatic complexity × fan-in/out × commit count: data-driven priority queue for vibe-code cleanup.
- [[tracer-bullets]] — 🆕 <span style="color:red">Make the agent deliver one thin vertical slice end-to-end and review it before widening, against its token-cheap layer-by-layer default.</span>
- [[red-green-tdd]] — 🆕 <span style="color:red">Agent proves every change with a test red before, green after; cures premature fixation; recipes for bugs, ATDD, fast inner loop.</span>
- [[spec-afterlife]] — 🆕 <span style="color:red">After shipping, each spec file drains to Jira, tests or one-line ADRs, then is deleted within ≈3 months.</span>
- [[record-replay-refactoring]] — 🆕 <span style="color:red">Record production inputs/outputs, replay them against the AI-rewritten version and diff — a characterization-test oracle for legacy rewrites.</span>
- [[mutation-testing]] — 🆕 <span style="color:red">Break production code on purpose to prove tests verify behaviour; PIT/Stryker, manual variant, where to aim it, cost and triage.</span>
- [[black-box-testing]] — 🆕 <span style="color:red">Test strategy for agent code: black-box component tests at the service seam, honeycomb, mocks lie, fixture trees, coverage targets.</span>
- [[file-based-testing]] — 🆕 <span style="color:red">Data-transformation tests as in/out file pairs run by one generic test, cheap for agents to mass-maintain and readable by business.</span>
- [[test-case-doc-automation]] — 🆕 <span style="color:red">Turn legacy Word/Excel test cases into WebdriverIO/Playwright suites with phrase-to-assertion traceability, then delete the doc.</span>
- [[agent-evals]] — 🆕 <span style="color:red">Testing agents as suites: scripted checks first, rubric LLM judge for free text, measure the bill, beware eval awareness.</span>
- [[chatops-agent]] — 🆕 <span style="color:red">Agent as a Slack/Teams team member that executes authorised work end-to-end; three maturity levels, whose-tokens budget, gateways, Claude Tag.</span>
- [[enterprise-architect-agent]] — 🆕 <span style="color:red">Background long-context agent cross-checking all services' contracts in a central repo to flag cross-service drift.</span>
- [[ai-use-cases-beyond-coding]] — 🆕 <span style="color:red">High-ROI agent chores around the code: flaky tests, release notes, docs, ticket→PR, in-house agent gallery, rollout path.</span>
- [[document-extraction-agents]] — 🆕 <span style="color:red">Extracting structured data from documents with one agent per type, per-field confidence, no required fields, two-model cross-check.</span>

## Concepts — data quality & observability
- [[data-quality-validator]] — programmatic validator (known input → assert output) beats agent eyeballing; latency metric companion.
- [[telemetry-by-default]] — 🆕 <span style="color:red">Ship zero-code OpenTelemetry + LGTM stack and RED dashboards from day one so humans and agents can observe the running system.</span>
- [[chatbot-latency-tuning]] — 🆕 <span style="color:red">Fixing a slow LLM chatbot from traces: traceparent into tools/LLM, programmatic pre-loading, parallel tool calls, streaming for perceived latency.</span>

## Concepts — database & search
- [[keyset-pagination]] — cursor-based pagination for 100K+ rows; OFFSET breaks at scale; stable tie-breaker required.
- [[pg-trgm]] — Postgres trigram extension: GIN/GiST indexes for `LIKE '%term%'` substring search at scale.

## Sources
- [[2026-04-07-ai-product-team]] — AI-Assisted Coding @ a product team (2026-04-07 – 2026-04-09)
- [[2026-04-16-ai-safety-critical]] — AI-Assisted Coding @ a safety-critical systems company (2026-04-16 – 2026-04-17)
- [[2026-04-27-ai-healthcare-software]] — AI-Assisted Coding @ a healthcare-software company (2026-04-27, 2026-04-30)
- [[2026-05-04-ai-telecom]] — AI-Assisted Coding @ a telecom operator (2026-05-04 – 2026-05-05)
- [[2026-05-06-ai-ecommerce]] — AI-Assisted Coding @ an e-commerce company (2026-05-06, 2026-05-08)
- [[2026-05-11-ai-software-vendor]] — AI-Assisted Coding @ a software vendor (2026-05-11..12)
- [[2026-05-13-ai-it-services]] — AI Coding @ an IT services company (2026-05-13..14)
- [[2026-05-18-secure-coding-payments]] — Secure Coding @ a payments company (2026-05-18..22)
- [[2026-05-25-ai-government]] — AI-Assisted Coding @ a government organization (2026-05-25 – 2026-05-26)
- [[2026-05-27-ai-open-workshop]] — AI-Assisted Coding @ an open workshop (2026-05-27)
- [[2026-06-02-ai-open-workshop]] — AI-Assisted Coding @ an open workshop (2026-06-02)
- [[2026-06-05-ai-consultancy]] — AI-Assisted Coding @ a consultancy (2026-06-05)
- [[2026-06-08-ai-security-tools]] — AI-Assisted Coding @ a security-tools company (2026-06-08 – 2026-06-09)
- [[2026-06-10-spring-ai-open-workshop]] — Spring AI @ an open workshop — 2026-06-10
- [[2026-06-11-ai-mobile-gaming]] — AI Agentic Engineering @ a mobile-gaming studio — Day 1 (2026-06-11)
- [[2026-06-22-ai-sports-betting]] — AI-Assisted Coding @ a sports-betting company (2026-06-22 + 2026-06-25)
- [[2026-06-23-ai-device-maker]] — AI-Assisted Coding @ a device maker (2026-06-23..24)
- [[2026-06-26-ai-agentic-how]] — AI-Assisted Coding @ agentic.how (2026-06-26)
- [[2026-07-01-ai-java-community]] — AI-Assisted Coding @ a Java user community (2026-07-01)
- [[2026-07-03-ai-bank]] — AI-Assisted Coding @ a bank (2026-07-03)
- [[2026-07-06-ai-medical-diagnostics]] — AI-Assisted Coding @ a medical-diagnostics company, Day 3 (2026-07-06 → 2026-07-08)
- [[2026-07-09-ai-live-casino]] — AI-Assisted Coding @ a live-casino game producer (2026-07-09 → 2026-07-10)
- [[2026-07-20-ai-telecom]] — AI-Assisted Coding @ a telecom operator (2026-07-20 → 2026-07-21)
- [[2026-07-22-ai-agentic-how]] — AI-Assisted Coding @ agentic.how (2026-07-22)
- [[2026-07-23-ai-software-product]] — AI-Assisted Coding @ a software-product company (2026-07-23 → 2026-07-24)
- [[2026-07-30-ai-safety-critical]] — AI-Assisted Coding @ a safety-critical company (2026-07-30 → 2026-07-31)
- [[2026-08-03-ai-telecom-ops-qa]] — AI-Assisted Coding @ a telecom operator — ops + QA (2026-08-03 – 2026-08-04)
- [[2026-08-06-ai-bank]] — AI-Assisted Coding @ a bank (2026-08-06 – 2026-08-07)
- [[2026-08-10-ai-consultancy]] — AI-Assisted Coding @ a consultancy (2026-08-10 – 2026-08-11)
- [[2026-08-12-ai-bank]] — AI-Assisted Coding @ a bank (2026-08-12)
- [[2026-08-13-ai-insurer]] — AI-Assisted Coding @ an insurer (2026-08-13 – 2026-08-14)
- [[2026-08-27-ai-it-services]] — AI-Assisted Coding @ an IT services company (2026-08-27 – 2026-08-28)
- [[2026-08-31-ai-software-vendor]] — AI-Assisted Coding @ a software vendor (2026-08-31..09-01)
- [[2026-09-02-ai-insurer]] — Agentic Engineering @ an insurer (2026-09-02..03)
- [[2026-09-04-ai-software-company]] — AI-Assisted Coding @ a software company (2026-09-04, 2026-09-09)
- [[2026-09-07-ai-bank]] — AI-Assisted Coding @ a bank (2026-09-07..08)
- [[2026-09-10-testing-ai-bank]] — Testing with AI @ a bank (2026-09-10..11)
- [[2026-09-14-ai-industrial]] — AI-Assisted Coding @ an industrial-equipment company (2026-09-14..15)
- [[2026-09-16-ai-safety-critical]] — Agentic Engineering @ a safety-critical systems vendor (2026-09-16..17)
- [[2026-09-18-ai-agentic-how]] — Agentic Engineering @ agentic.how (2026-09-18)
- [[2026-09-24-ai-bank]] — AI-Assisted Coding @ a bank (2026-09-24)
- [[2026-09-25-ai-bank-migration]] — Agentic Coding @ a bank (2026-09-25)
- [[2026-09-28-ai-gaming-tech]] — AI Agentic Engineering @ a gaming-tech company (2026-09-28 – 2026-09-29)
- [[2026-09-30-ai-enterprise]] — AI-Assisted Coding @ an enterprise (2026-09-30, 2026-10-05)
- [[2026-10-01-ai-bank]] — AI Agentic Engineering @ a bank (2026-10-01 – 2026-10-02)
- [[2026-10-06-gray-factory-devoxx]] — Responsibly Dimming the Lights in the Software Factory @ Devoxx Belgium (2026-10-06)
- [[2026-10-07-agentic-reconversion-devoxx]] — Agentic Engineering Reconversion @ Devoxx Belgium (2026-10-07)
- [[2026-10-09-ai-jax-london]] — Agentic Engineering @ JAX London (2026-10-09)
- [[2026-10-13-ai-bank]] — AI-Assisted Coding Workshop @ a bank (2026-10-13)
