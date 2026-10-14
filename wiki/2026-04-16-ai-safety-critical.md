---
title: "Source: AI-Assisted Coding @ a safety-critical systems company (2026-04-16 – 2026-04-17)"
category: source
tags: [source, safety-critical-company, workshop, two-day, romanian, copilot, safety-critical]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day Romanian-language workshop for backend and frontend developers in a safety-critical domain, working mainly with GitHub Copilot in IntelliJ and VS Code under strict tool-approval rules. Day 1 toured real use cases and failures (the term "AI spiraling" was coined here), model tiers and plan mode, how the context window fills before you type, hierarchical AGENTS.md and skills, anti-sycophancy rules, garbage-in-garbage-out in legacy code, and the "code at night" vision. Day 2 opened with Anthropic's advisor pattern and task budgets, then went deep on OpenSpec (new + continue over propose, archive as a living spec), a client-voiced prompting interview and an OCD++ line-by-line spec review, the custom-skill rulebook, an AST-first OpenRewrite strategy for mass refactors, `/insights`, a chatbot-security session (Gandalf, attack catalog, tenant isolation enforced in the MCP, GitLeaks), architecture-as-code with PlantUML + ArchUnit, four layers of quality gates, and closed with a Q&A on sharing AI context across a monolith and ≈15 microservices.

## Concept inventory

*Context & models* — [[context-window]] (8% gone at "hello", yellow at 65%), [[prompt-caching]] (model switch = full re-pay), [[model-hierarchy]] (advisor pattern), [[token-economy]] (task budgets), [[harness]] (Copilot vs Claude Code), [[plan-mode]].

*Knowledge engineering* — [[agents-md-discipline]] (third-time rule, lessons-learned retrospective prompt, `architecture.md` / `testing.md` by reference), [[agent-skill]] (when to write one, 50–300 lines, ≈50–100 cap), [[self-improving-knowledge]] (`/insights`), [[light-dark-code]] (SQL injection from 1990s surroundings), [[cross-repo-knowledge]] (central contracts repo, rule promotion), [[enterprise-architect-agent]], [[memory-layers]].

*Spec-driven development* — [[spec-driven-development]] (context lives in git, not chat), [[openspec]] (five commands, portability caveat, never hand-edit), [[grill-me]] (prompt as the client, don't cut the AI's dreams), [[mob-spec-review]] (OCD++), [[non-goals]] (over-engineering cut), [[ai-never-deletes]], [[superpowers]].

*Feedback & tools* — [[feedback-loop]] (UI without a browser check ships broken), [[browser-mcp]] (Chrome DevTools MCP), [[figma-mcp]] (Stitch), [[openrewrite]] (recipe-first refactoring, the Opus-session-to-recipe combo), [[ai-spiraling]].

*Security* — [[jailbreak-ladder]] (Gandalf acrostic), [[prompt-injection]] (attack catalog, safety-classifier false positive, extension compromise), [[mcp-tenant-isolation]], [[supply-chain-attack]] (slopsquatting, GitLeaks), [[data-governance]] (ZDR via Bedrock, air-gapped option), [[production-safety]], [[reward-hacking]] (Dieselgate analogy).

*Architecture & quality gates* — [[c4-model]], [[archunit-drift-control]] (ArchUnit importing PlantUML), [[codeowners-elders]], [[enforcement-ladder]] (four layers of defense in depth), [[acceptance-test-bdd]] (executable specs), [[ralph-loop]] ("code at night").

## See also
- [[2026-04-07-ai-product-team]]
- [[2026-04-27-ai-healthcare-software]]
- [[2026-05-04-ai-telecom]]
