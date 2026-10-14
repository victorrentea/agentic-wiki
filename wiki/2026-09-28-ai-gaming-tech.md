---
title: "Source: AI Agentic Engineering @ a gaming-tech company (2026-09-28 – 2026-09-29)"
category: source
tags: [source, gaming-tech, workshop, two-day]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day agentic-engineering workshop on the public petclinic course repo. Day 1 covered context and token efficiency (status-line burn gauges, cache economics, a 70k-token "hi"), where team knowledge should live (AGENTS.md hygiene, goal-not-instruction skills, three-tier plugin marketplaces with a feedback skill), debugging loops (Jira2PR with a Playwright CLI) and spec-driven development up to a live OpenSpec run on a grilled pagination ticket. Day 2 went from agent tools and Slack-bot orchestration through human review (reviewer agents, `review.md` handoff, generated drift-proof review artefacts, Code City), testing strategy (API acceptance tests over mock-heavy unit tests) and AI security (MCP identity, uncensored models, prompt-injection entry points, sandboxes, the Railway prod deletion). The source also carries a fact-check section that corrects several claims now in the wiki (thinking blocks, npm postinstall, WebFetch).

## Concept inventory

*Context & cost* — [[context-window]] (thinking blocks now preserved), [[prompt-caching]] (subscription ≈60× API value, 5-min Copilot cache), [[token-economy]] (tool output dominates), [[effort-setting]] (high is the ceiling), [[dumb-zone]] (65–85%, compaction nuance), [[handover]] (resume / compact / handover), [[rtk]] (prefer native terse flags).

*Knowledge & skills* — [[agents-md]] (`@AGENTS.md` import), [[agents-md-discipline]] / [[claude-md]] (8,000-line anti-example, doc gardening), [[agent-skill]] (goals not instructions, path-triggered skills), [[tripwire]], [[skills-governance]] / [[plugin-marketplace]] (company/team/private marketplaces, REVIEW.md), [[skill-marketplace-security]], [[self-improving-knowledge]] (second brain), [[two-layer-memory]] (output styles).

*Harness & tools* — [[harness]] (vendor-trained harness, steer in flight), [[model-hierarchy]] / [[sub-agents]] (pick by how late mistakes surface), [[cli-vs-mcp-tradeoff]], [[model-context-protocol]], [[agent-lifecycle-hooks]], [[ci-green-loop]], [[code-graph]] (LSP), [[profiling-agents]], [[firefighter-agent]] (Slack team bot).

*Specs & review* — [[grill-me]] (warm up first, stop after two boring questions), [[spec-driven-development]] / [[openspec]] / [[superpowers]] (spec afterlife, vertical slices), [[multi-model-review]] / [[code-review]] (P(slop)², one round, 20 clean PRs), [[assumptions-ledger]], [[review-by-images]], [[field-reality-diagram]] / [[c4-model]], [[code-city]], [[acceptance-test-bdd]], [[cognitive-debt]] / [[skill-erosion]].

*Security* — [[agent-auth]] (JWT human + API-key agent), [[guardrails]] / [[judge-llm]] / [[jailbreak-ladder]] (authorize at the tool, RLS), [[prompt-injection]] (logs, faint screenshots, image-URL exfil), [[lethal-trifecta]], [[supply-chain-attack]] / [[slopsquatting]] (pnpm 10), [[uncensored-models]], [[agent-permissions]] / [[approval-fatigue]] / [[os-sandbox]] / [[docker-sandboxing]] (three sandboxes, credential masking), [[production-safety]], [[data-governance]].

*Role* — [[agentic-engineering]] (coding is solved, humble prompting), [[vibe-coding]], [[vibe-fixer]] (business PRs), [[agentic-corporation]].

## See also
- [[2026-09-25-ai-bank-migration]]
- [[2026-09-30-ai-enterprise]]
- [[2026-06-23-ai-device-maker]]
- [[multi-model-review]]
