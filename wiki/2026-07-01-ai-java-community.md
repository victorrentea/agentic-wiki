---
title: "Source: AI-Assisted Coding @ a Java user community (2026-07-01)"
category: source
tags: [source, java-community, workshop, one-day, netherlands]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day in-person workshop for a Dutch Java user community, run live on Victor Rentea's petclinic and training-assistant repos. The arc climbed the adoption ladder (IDE plugin → agentic CLI → agentic in production), then went through CLAUDE.md curation, skills and progressive disclosure, giving the agent the same tools a human dev uses (MCP vs CLI), spec-driven work with OpenSpec, guardrails, generated diagrams and sorted review, and closed on security: being "SBOMbed" through the supply chain, prompt injection, and containing a rogue agent so YOLO can run safely in a box. It added new numbers (skill descriptions of 60–300 tokens, 36% of hub skills vulnerable, a $20 review budget, a 100k-token slop threshold) and a run of security war stories.

## Concept inventory

*Foundations* — [[harness]] (adoption ladder, the leaked harness prompts), [[model-hierarchy]] (Opus architect / Sonnet laundry / GPT critic), [[context-window]] (200k healthy default), [[dumb-zone]], [[token-economy]] (paying for discarded thinking, `/context` every sprint), [[prompt-caching]] (subscription vs API-key ladder), [[data-governance]].

*Knowledge engineering* — [[claude-md]] (must-follow markers, `CLAUDE.local.md`, refresh from the git diff, €1-per-deleted-line), [[agents-md]], [[progressive-disclosure]], [[agent-skill]] (description length, deterministic phrasing, Skill-tool hook layering), [[custom-agent]], [[self-improving-knowledge]] (JSONL introspection, Hermes), [[sycophancy]].

*Requirements & specs* — [[grill-me]] (the fourth option, conclusions.md fast-forward, trunk-vs-leaf questions), [[spec-driven-development]] (spec expiry, Jira2PR for trivial work), [[openspec]] (explore, archive), [[superpowers]], [[non-goals]], [[acceptance-test-bdd]], [[ralph-loop]], [[plan-mode]].

*Tools & feedback loops* — [[cli-vs-mcp-tradeoff]], [[model-context-protocol]], [[browser-mcp]], [[observability-mcp]], [[profiling-agents]], [[firefighter-agent]], [[ci-green-loop]], [[feedback-loop]], [[chop-ai-waiter]].

*Review & visualization* — [[code-review]] ("second reviewer" anti-pattern, conventional commits, $20 review budget), [[multi-model-review]], [[static-analysis-gauntlet]], [[archunit-drift-control]] (PlantUmlArchCondition, hooksPath gotcha), [[field-reality-diagram]] (browser-initiated OTel), [[review-by-images]], [[code-city]], [[bug-heatmap]], [[codeowners-elders]] (unversioned dependency set), [[agentic-corporation]] (the review "purgatory").

*Security* — [[supply-chain-attack]] (Axios, internal mirrors, VS Code extensions), [[skill-marketplace-security]], [[prompt-injection]] and [[tool-poisoning]] (tool-description poisoning, RGB-253 text), [[lethal-trifecta]], [[jailbreak-ladder]] (cross-vendor juggling), [[production-safety]], [[elicitation]], [[agent-permissions]] (make the safe way the comfortable way), [[os-sandbox]], [[docker-sandboxing]] (claude-docker), [[secret-zero]] (Bromure), [[agent-lifecycle-hooks]].

*Human factors* — [[skill-erosion]], [[ai-addiction]] (agency depletion), [[cognitive-debt]], [[wispr-flow]], [[agentic-engineering]].

## See also
- [[2026-06-26-ai-agentic-how]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-22-ai-sports-betting]]
