---
title: "Source: AI Coding @ an IT services company (2026-05-13..14)"
category: source
tags: [source, it-services, workshop, two-day, copilot-cli, dotnet, openspec, security, guardrails]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop for a .NET / Angular team using GitHub Copilot CLI as the harness over Anthropic models (the transcript is in Romanian). Day 1 covered how the model sees the world (tokens, model tiers, what fills the context window before you type, the dumb zone and the 5-minute cache), plan mode with parallel sub-agents, word-by-word plan review, red-green TDD, catching façade tests with manual mutation, hierarchical AGENTS.md, and Superpowers brainstorming as a context firewall on a pagination task. Day 2 opened with a recall quiz, ran a full OpenSpec cycle (estimated at two weeks, delivered in about half an hour), then moved to AI security (RLHF and its bypasses, the emoji attack, the lethal trifecta, markdown-image exfiltration, poisoned screenshots, Gandalf, the tax-office model-swap attack, slopsquatting) and to guardrails: a domain model reflected into PlantUML on every commit, ArchUnit against a hand-drawn package diagram, CODEOWNERS as the third wall, the OS sandbox, and continuous red-blue teaming.

## Concept inventory

*Fundamentals* — [[context-window]] / [[dumb-zone]] (compaction fired at ≈67%), [[prompt-caching]], [[model-hierarchy]] (Gemini's niche), [[effort-setting]], [[ollama]] / [[data-governance]] (weights vs temperature, open-weight ≠ open-source), [[token-economy]] (128-tool cap, Copilot token billing, token padding), [[caveman]], [[hallucination]] (ask for proof), [[sycophancy]] (firm disappointment, "option 4"), [[wispr-flow]] (prompt language), [[ai-addiction]] (AI psychosis).

*Planning & specs* — [[plan-mode]] (prompt-quality ladder), [[sub-agents]] (three orchestration strategies, context firewalls, research as synthetic wiki), [[superpowers]], [[grill-me]], [[spec-driven-development]] (300-line cap, estimation collapse), [[openspec]], [[non-goals]], [[ralph-loop]], [[agents-md]] (verify the hierarchy).

*Testing* — [[red-green-tdd]], [[mutation-testing]], [[acceptance-test-bdd]], [[record-replay-refactoring]], [[browser-mcp]] (exploring, not testing).

*Security* — [[jailbreak-ladder]] (repetition, guilt attack, emoji attack paper), [[lethal-trifecta]], [[prompt-injection]] (extract with a script instead of reading HTML; screenshot steganography caveat), [[model-context-protocol]], [[observability-mcp]], [[secret-zero]], [[slopsquatting]], [[skill-marketplace-security]], [[supply-chain-attack]], [[agent-permissions]] / [[approval-fatigue]], [[os-sandbox]], [[docker-sandboxing]] (red-blue teaming).

*Guardrails* — [[archunit-drift-control]] (three walls, the diagram is the truth), [[deterministic-diagrams]], [[codeowners-elders]], [[determinism-over-llm]] (auto-fix vs critique), [[code-review]] ("build the fences as a senior"), [[agentic-engineering]].

## See also
- [[2026-05-06-ai-ecommerce]]
- [[2026-05-11-ai-software-vendor]]
- [[2026-05-18-secure-coding-payments]]
- [[archunit-drift-control]]
