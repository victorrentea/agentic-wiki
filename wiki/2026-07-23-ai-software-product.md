---
title: "Source: AI-Assisted Coding @ a software-product company (2026-07-23 → 2026-07-24)"
category: source
tags: [source, software-product, workshop, two-day, hands-on]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop for ≈28 engineers at a software-product company, with one through-line: the agent is only as good as the context you give it — and building that context is now the job. Day 1 went from where the industry stands in 2026 (Opus 4.5 as the inflection point, the "flat curve" of laggers) through what travels with every prompt, skills and their frontmatter, CLAUDE.md hygiene ("never write 'we removed X'"), expertise extraction, grilling a two-line ticket (question count sizes the story), and spec-driven implementation reviewed as a product owner. Day 2 covered MCP internals and least privilege, guardrails vs tripwires and deterministic refactoring recipes, reviewing the plan before the code, the agentic PR loop and overnight CI, business vibe-coding, then turned every team loose to vibe-code a real chore on its own project — including a feedback-loop masterclass migrating a home-grown schema migrator to Liquibase. It also carries several verified corrections (Shai-Hulud vs axios, skill `paths` semantics, `copilot-instructions.md`, auto-memory location).

## Concept inventory

*Foundations & economics* — [[model-hierarchy]] (Opus 4.5 inflection, Opus-until-quota), [[harness]], [[claude-code-router]], [[token-economy]] (MCP schema rent, seat vs API), [[context-window]], [[dumb-zone]], [[prompt-caching]], [[sycophancy]], [[rtk]], [[headroom]], [[ai-adoption]].

*Knowledge engineering* — [[claude-md]] (tool it up, never write "we removed X"), [[agents-md]] (copilot-instructions not dead), [[memory-layers]], [[two-layer-memory]], [[agent-skill]] (`paths` narrows, `disallowed-tools`), [[skill-marketplace-security]], [[vibe-heresy]], [[decision-context]] (3-month expiry into an ADR table), [[light-dark-code]] (finish half-done migrations), [[plugin-marketplace]], [[cross-repo-knowledge]].

*Requirements & specs* — [[grill-me]] (question count sizes the story, persona ladder, humans still beat the grill), [[spec-driven-development]] (frameworks converge, "cuatro amigos"), [[openspec]], [[non-goals]], [[three-amigos]], [[mob-spec-review]], [[ralph-loop]] (skip it with Opus as manager), [[superpowers]].

*Feedback loops & tests* — [[feedback-loop]] (prove-it loop as the #1 concern), [[acceptance-test-bdd]] (load tests as acceptance, flaky = race), [[browser-mcp]], [[figma-mcp]], [[ci-green-loop]], [[ai-use-cases-beyond-coding]].

*Review & architecture* — [[multi-model-review]] (review the plan, read-only reviewers), [[code-review]] ("I want your brain, not your tokens"), [[assumptions-ledger]], [[policeman-agent]], [[static-analysis-gauntlet]], [[tripwire]], [[codeowners-elders]], [[archunit-drift-control]] (fix the code, not the diagram), [[openapi-contract-sync]], [[code-city]], [[c4-model]], [[field-reality-diagram]].

*Legacy* — [[legacy-migration-stages]] (Liquibase masterclass, delete-and-reconstruct), [[openrewrite]], [[mutation-testing]], [[vibe-fixer]] (agree the fix cost before revealing AI).

*MCP & safety* — [[model-context-protocol]], [[mcp-sandbox-inheritance]] (run the harness as a restricted user), [[elicitation]], [[sampling]], [[cli-vs-mcp-tradeoff]], [[production-safety]] (Railway/Cursor, kill at ½M tokens), [[supply-chain-attack]] (Shai-Hulud vs axios), [[docker-sandboxing]].

*Human factors* — [[skill-erosion]] (10× learner, 1.3× doer), [[gray-factory]], [[agentic-engineering]].

## See also
- [[2026-07-22-ai-agentic-how]]
- [[2026-07-20-ai-telecom]]
- [[2026-07-01-ai-java-community]]
