---
title: "Source: AI-Assisted Coding @ an open workshop (2026-05-27)"
category: source
tags: [source, open-workshop, workshop, one-day, english]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

One-day English workshop for a mixed-stack (Java, C#, Go), mostly senior group, run on a Spring Boot/Angular PetClinic with live demos and heavy debate. It moved from fundamentals (harness vs model, token prices and where tokens really go, the dumb zone, the 5-minute cache) through sub-agents, worktrees, skills and MCP design, to the multi-model review pipeline seen converging at three companies, OpenSpec on a real ticket, and a security block (supply chain, prompt-injection vectors, the lethal trifecta, sandbox vs Docker, permission settings). Memorable war stories: a CI agent that whitelisted itself by writing a helper script, and the Railway volume deletion read as a least-privilege failure.

## Concept inventory

*Economics* — [[harness]], [[token-economy]] (Opus ≈5× Sonnet in practice), [[model-hierarchy]], [[toon]], [[prompt-caching]] (TTL dropped to 5 min on 2 April 2026), [[dumb-zone]] (needle-in-haystack figures).

*Knowledge* — [[claude-md]] / [[agents-md-discipline]] (pain-fed, pruned every sprint), [[progressive-disclosure]] (nested files, `@` as forced load), [[agent-lifecycle-hooks]] (rewrite commands, don't beg), [[agent-skill]] (30–80 lines, front-matter controls), [[skill-marketplace-security]] / [[tool-poisoning]], [[plugin-marketplace]], [[cross-repo-knowledge]] (contracts, not clones), [[self-improving-knowledge]] (`/insights`).

*Delegation* — [[sub-agents]] (≈20k overhead, Kanban waves), [[worktree]], [[plan-mode]], [[browser-mcp]].

*Specs & review* — [[grill-me]] (phone-book discovery), [[openspec]] (archive, feature removal), [[superpowers]], [[non-goals]], [[acceptance-test-bdd]], [[agentic-review-pipeline]] / [[multi-model-review]] (8 reviewers, resume-on-CI-failure, heat-map review).

*MCP* — [[model-context-protocol]] (MCP vs REST, UX lessons), [[elicitation]] (`destructiveHint` is not a gate), [[cli-vs-mcp-tradeoff]], [[production-safety]] (Railway post-mortem).

*Security* — [[supply-chain-attack]], [[slopsquatting]], [[prompt-injection]] (adversarial suffix, HTML comments, steganography), [[lethal-trifecta]], [[os-sandbox]], [[docker-sandboxing]], [[agent-permissions]], [[dont-run-in-home-folder]].

*Architecture & people* — [[deterministic-diagrams]] / [[c4-model]] (PlantUML labels for *why*), [[code-graph]] (Serena), [[skill-erosion]] (AI Vampire, redemption sprint), [[system-2-fatigue]], [[cognitive-debt]], [[vibe-fixer]] (forward-deployed engineer).

## See also
- [[2026-06-02-ai-open-workshop]]
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-05-25-ai-government]]
