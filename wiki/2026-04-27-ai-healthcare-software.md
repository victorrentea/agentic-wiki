---
title: "Source: AI-Assisted Coding @ a healthcare-software company (2026-04-27, 2026-04-30)"
category: source
tags: [source, healthcare, workshop, two-day, copilot, windsurf, openspec, modulith]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop, three days apart so pairs could attempt an OpenSpec homework, for ≈14 mid-senior backend, frontend and QA engineers on a modular monolith with satellite services, using Copilot (editor and CLI), Windsurf and on-prem Jira/GitLab behind internal package mirrors. Day 1 moved AI "from advisor to executor": a brutal one-shot search prompt and its review (a silently skipped requirement, index-killing SQL), writing AGENTS.md rules together, the slash-command / skill / custom-agent trichotomy, a corporate → product → repo knowledge hierarchy, MCP in depth with a supply-chain warning, and a gallery of in-house agents (firefighter, newcomer trainer). Day 2 reviewed the homework and went deep into spec-driven development — vibe heresy, OpenSpec mechanics, System 1 vs System 2 and fatigue-LGTM, mob-reviewing a spec, chaining brainstorm → handover → OpenSpec — then advisor vs executor, skills and `/context`, orchestration patterns, legacy refactoring (OpenRewrite, characterization tests from production recordings), the boring chores AI is best at, worktrees and agency depletion, Gandalf, and approval fatigue with the March 2026 Axios worm.

## Concept inventory

*Mindset* — [[advisor-vs-executor]], [[system-2-fatigue]], [[ai-addiction]] (agency depletion: ≤2 agents, ≤1 deep), [[vibe-coding]] (prototype with 2–3 vibes, then productionalize), [[vibe-heresy]], [[ai-never-deletes]].

*Knowledge engineering* — [[agents-md-discipline]] (cross-cutting facts at the top level, positive phrasing, paired cross-role rules, volumetry), [[memory-layers]] (corporate → product → repo, top-down pull + bottom-up promotion), [[skills-governance]] (skill-extension problem, build your own), [[agent-skill]] (scripts over tokens, "I know Kung Fu"), [[custom-agent]], [[agent-lifecycle-hooks]] (`SessionStart` refresh), [[token-economy]] (`/context`, tool-definition bloat).

*Spec-driven development* — [[openspec]] (init drops files, new + continue, the hidden `specs/` file, archive), [[spec-driven-development]] (how detailed a design should be, SDD > Superpowers > direct), [[mob-spec-review]], [[grill-me]], [[handover]] (brainstorm → handover.md → OpenSpec), [[superpowers]], [[example-driven-prompting]].

*Feedback & testing* — [[feedback-loop]] (ask for proof, "prove me wrong" load test), [[acceptance-test-bdd]] (feature-file critique, QA Jira → Cypress flow), [[browser-mcp]] (exploratory-testing prompt), [[mutation-testing]], [[legacy-migration-stages]] (production-recorded characterization tests, fuzzy tests), [[openrewrite]].

*MCP & ops* — [[model-context-protocol]], [[cli-vs-mcp-tradeoff]] (`gh` beats the GitHub MCP), [[observability-mcp]] (Grafana trace traversal, `COMMENT ON COLUMN` for AI), [[code-graph]] (Serena, CodeGraphContext, LSP), [[firefighter-agent]] (guardrails scripted in the tool), [[ai-use-cases-beyond-coding]].

*Security* — [[supply-chain-attack]] (`npx @latest`, Axios worm, mirror lag), [[lethal-trifecta]] (practitioner variant), [[agent-permissions]] (approval fatigue), [[docker-sandboxing]], [[jailbreak-ladder]], [[prompt-injection]], [[worktree]].

## See also
- [[2026-04-07-ai-product-team]]
- [[2026-04-16-ai-safety-critical]]
- [[2026-05-04-ai-telecom]]
