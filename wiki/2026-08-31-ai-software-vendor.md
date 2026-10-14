---
title: "Source: AI-Assisted Coding @ a software vendor (2026-08-31..09-01)"
category: source
tags: [source, software-vendor, workshop, two-day, onsite]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (in Romanian) with a team of product developers, worked live on the petclinic demo app. Day 1 took the group from "slave mode" (copy-pasting into a chat) to agents that run on their own: context anatomy and token economics, rules vs tripwires, skills and hooks, a grilled and spec-driven paginated-grid feature, review agents, and guardrails next to production (with the Railway volume-deletion post-mortem). Day 2 took agents out of the laptop into the organization: support-ticket agents and their three accesses, MCP's three justifying features, injection through logs and form fields, useless vs real guardrails, a script-generated human-review dashboard, mutation testing, testing seams, the sandbox ladder up to microVMs with fake credentials, and how to keep human skills from rusting.

## Concept inventory

*Context & cost* — [[context-window]] anatomy (≈50k on "hello"), [[dumb-zone]] at 65–85%, compaction as a one-page CV, [[handover]], [[prompt-caching]] (5-min TTL, sub-agents/CI waits outliving the parent's cache, subscription ≈40× cheaper than API), [[token-economy]] (Tool Search Tool, English vs Romanian, Anthropic's token-saving guide), [[sub-agents]] (Explore vs `fork`), [[model-hierarchy]] (Opus-on-medium beats Sonnet), [[harness]] beats model, [[claude-code-router]].

*Knowledge engineering* — [[claude-md]] (≤60–100 lines, Boris's file), [[agents-md]], [[agents-md-discipline]] (anger-driven rules, markdown-diff retros, doc-gardening loop), [[tripwire]] (don't mention the enemy, GIGO), [[light-dark-code]], [[agent-skill]] (60–300-word descriptions, commands = `disable-model-invocation`), [[self-improving-knowledge]] (30-day conversation deletion), [[elder-wisdom-mining]], [[plugin-marketplace]], [[agent-lifecycle-hooks]].

*Requirements & specs* — [[grill-me]] (≈35 questions, functional first), [[plan-mode]] vs spec thresholds, [[spec-driven-development]] (proposal sections, burn after 3 months, state machines/formal cores), [[openspec]], [[superpowers]], [[non-goals]], [[decision-context]], [[keyset-pagination]], [[hallucination]] (give tools to verify reality).

*Review & testing* — [[code-review]] (gradual trust, PR-size heuristics, REVIEW.md), [[multi-model-review]] (adversarial math, slop "brain surgery"), [[stacked-prs]], [[human-review-dashboard]], [[review-by-images]], [[field-reality-diagram]] (N+1 caught), [[deterministic-diagrams]], [[archunit-drift-control]], [[codeowners-elders]] (six-eyes), [[ci-green-loop]], [[acceptance-test-bdd]] (testing seam, secret glue-vs-impl agents), [[mutation-testing]], [[semantic-coverage]], [[bug-heatmap]] (PR risk classifier), [[code-city]], [[code-graph]] (eval on your project), [[skills-benchmarking]].

*Operating agents* — [[chop-ai-waiter]] (zero paste), [[feedback-loop]] (before/after video), [[agentic-corporation]] (StrongDM dark factory), [[firefighter-agent]], [[observability-mcp]], [[profiling-agents]], [[chatops-agent]], [[model-context-protocol]] (three features, MCP gateway), [[mcp-transport]], [[agentic-engineering]] (DevEx survey), [[ai-calisthenics]].

*Security* — [[production-safety]] (PocketOS/Railway), [[agent-permissions]] ([[approval-fatigue]]), [[docker-sandboxing]] (Docker Sandboxes, fake keys), [[secret-zero]], [[prompt-injection]] (logs, split attack), [[geo-steganography]], [[browser-mcp]] (a11y tree = no steganography), [[lethal-trifecta]] (no curl), [[jailbreak-ladder]], [[slopsquatting]], [[data-governance]].

*Human factors* — [[sycophancy]] ("make no mistakes" studies), [[skill-erosion]] (tiny examples, planted mistakes, agentic interviews), [[ai-addiction]] (AI Vampire), [[vibe-coding]], [[wispr-flow]].

## See also
- [[2026-06-26-ai-agentic-how]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
