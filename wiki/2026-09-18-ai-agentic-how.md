---
title: "Source: Agentic Engineering @ agentic.how (2026-09-18)"
category: source
tags: [source, agentic-how, workshop, one-day, remote, open]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day open workshop with 27 engineers from across Europe, run entirely inside the `petclinic` Spring Boot + Angular repository. The arc: pick your tool and pay for it honestly (quota maths, model tiers including Fable and Luna, the harness a model was trained in), feed the agent the right context and nothing more (AGENTS.md for the weakest model, rules vs skills, a 70k-token start-up bill, under 40 skills), make knowledge shareable (team-git plugin marketplace, feedback skill, skill evals), and stop trusting it blindly (approval fatigue, Docker sandboxes, grill-me, OpenSpec as scaffolding to rehouse and delete, a second LLM reviewing with a fixed/declined/assumptions report, guided review through generated pictures, CODEOWNERS on guardrails). Its fact-check section corrected several claims repeated across sessions: the 5-minute cache TTL is a default not a cap, thinking is stripped only from previous turns, the skills-malware share is 5.2% malicious / 26.1% vulnerable, and the ten-approvals threshold is unsourced.

## Concept inventory

*Cost & models* — [[token-economy]] (credits per day, Luna ≈1/40 Sonnet, 70k start-up), [[model-hierarchy]] (Fable, Haiku/Luna), [[effort-setting]] (Opus 5 medium ≈ Opus 4 high), [[prompt-caching]] (default not cap), [[harness]] (model trained in its harness), [[data-governance]] (shadow AI).

*Context engineering* — [[agents-md]], [[agents-md-discipline]] (write for the weakest model, ≈50 lines), [[agent-skill]] (rules vs skills, under 40 skills), [[progressive-disclosure]], [[context-window]] (thinking preservation), [[caveman]], [[cli-vs-mcp-tradeoff]] (MCP only for cloud or locked-down agents).

*Sharing knowledge* — [[plugin-marketplace]], [[skills-governance]] / [[cross-repo-knowledge]] (PostToolUse skill extension, team-approved skill changes after an eval), [[skill-marketplace-security]] (SkillSpector numbers).

*Workflow* — [[feedback-loop]] (Jira→PR with the IDE never opened), [[grill-me]], [[spec-driven-development]] (markdowns as scaffolding: rehouse into Jira, pipeline tests and ADRs), [[non-goals]], [[light-dark-code]] (greenfield reference repo), [[ci-green-loop]].

*Review & guardrails* — [[multi-model-review]] (REVIEW.md, ≈€20 per 10k-line PR, fresh agent after a long build), [[assumptions-ledger]], [[code-review]] (stacked PRs, live branch stack, cost per phase), [[review-by-images]], [[deterministic-diagrams]], [[codeowners-elders]], [[tripwire]], [[approval-fatigue]], [[docker-sandboxing]].

*Human factors* — [[vibe-coding]], [[agentic-engineering]] (one-pizza trio), [[vibe-fixer]], [[skill-erosion]] (weekly `learning` output style, snippet repo).

## See also
- [[2026-06-26-ai-agentic-how]]
- [[2026-09-16-ai-safety-critical]]
- [[2026-09-24-ai-bank]]
