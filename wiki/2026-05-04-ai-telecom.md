---
title: "Source: AI-Assisted Coding @ a telecom operator (2026-05-04 – 2026-05-05)"
category: source
tags: [source, telecom, workshop, two-day, romanian, claude-code, claude-code-router, openspec]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day Romanian-language workshop (room + remote) for a telecom team that switched, during the course, from Copilot and Cline to Claude Code routed through an internal LLM proxy with a fixed budget. Day 1 fought corporate-network installation, then covered LLM fundamentals and token prices, three causes of hallucination with a "prove your sources" trick, the 65% context threshold and manual handover, CLAUDE.md vs AGENTS.md, brainstorm-then-TDD, the four-stage escalation from hand-fixing to guardrails, what survives between turns, a six-step recovery from AI spiraling, hierarchical memory and thin vs fat skills, a live Superpowers exercise, and MCP. Day 2 (morning only in the transcript) set four tiers of working with AI by task size, critiqued Opus-in-Copilot, put OpenRewrite before AI for migrations, named vibe heresy, dissected OpenSpec and its archive, traced a requirement hallucination back to training data, and closed with TOON, the Ralph loop, System 1 vs System 2, URL-state UX polish, LSP-assisted dead-code detection and a domain glossary.

## Concept inventory

*Foundations* — [[harness]] (Claude Code > OpenCode > Cline > Copilot), [[claude-code-router]], [[context-window]] (thinking kept within a turn, dropped between turns), [[prompt-caching]] (plan with Opus, execute with Sonnet in a new conversation), [[effort-setting]], [[knowledge-cutoff]], [[hallucination]] (Amazon-link proof, glossary, training-data drift), [[token-economy]] (ccusage, Java vs Python token cost), [[toon]].

*Steering & knowledge* — [[sycophancy]] (two golden rules), [[ai-spiraling]] (six-step recovery), [[enforcement-ladder]] (steering → memory → guardrail), [[agents-md]] (CLAUDE.md vs AGENTS.md caveat), [[claude-md]], [[agent-skill]] (thin vs fat, ≈50-skill paralysis), [[example-driven-prompting]], [[agentic-engineering]] ("ich will").

*Spec-driven development* — [[grill-me]] (brainstorm-then-TDD prompt, squeeze your brain first, Jira flow), [[superpowers]], [[sub-agents]] (self-contained briefs, research), [[spec-driven-development]] (four tiers by task size), [[openspec]] (new + continue, granularity, archive), [[vibe-heresy]], [[ralph-loop]], [[system-2-fatigue]].

*Tools & legacy* — [[model-context-protocol]] (five features, official catalog), [[browser-mcp]] (bug-reproduction loop), [[observability-mcp]], [[openrewrite]], [[legacy-migration-stages]] (tests are the PL/SQL migration blocker), [[code-graph]] (LSP find-references), [[ai-never-deletes]], [[figma-mcp]], [[wispr-flow]], [[keyset-pagination]] (replace, not push).

## See also
- [[2026-04-07-ai-product-team]]
- [[2026-04-16-ai-safety-critical]]
- [[2026-04-27-ai-healthcare-software]]
