---
title: "Source: AI-Assisted Coding @ a telecom operator (2026-07-20 → 2026-07-21)"
category: source
tags: [source, workshop, two-day, romanian, telecom]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day workshop (taught in Romanian) for a telecom operator's development group, live on petclinic with Claude Code and Copilot CLI. Day 1 laid the foundations — vibe coding redefined as *not reading* the code, LLM vs harness (JRE vs JDK), AGENTS.md and skills, tokens and money (where the money is really lost, the cost of `/clear`), hallucination and safe web retrieval, the agentic loop and the three precision levels — and set up the case study: paginating and sorting the Owners grid, grilled against a read-only QA database. Day 2 put it into practice: word-by-word review of the spec-driven docs, Playwright test agents and OTel-generated sequence diagrams, multi-agent code review in a fresh conversation, jailbreaks and prompt-injection variants, and wiring the agent into the enterprise (NTLM proxy, CI, read-only DB, Jira) before running it autonomously in a Docker sandbox. Its correction untangles the Shai-Hulud npm worm from the Axios story.

## Concept inventory

*Foundations* — [[vibe-coding]] (not reading the code), [[harness]] (JRE/JDK analogy, corporate proxy), [[model-hierarchy]], [[context-window]] (context beats model, cost of `/clear`), [[token-economy]] (where money is lost), [[prompt-caching]], [[dumb-zone]], [[hallucination]] (training-data poisoning), [[knowledge-cutoff]], [[context7]].

*Knowledge engineering* — [[agents-md-discipline]] (add a line only on a red-handed catch, reference `openapi.yaml`), [[agents-md]], [[agent-skill]], [[deterministic-diagrams]], [[c4-model]].

*Requirements & specs* — [[grill-me]] (plan mode, "No", read-only QA DB), [[spec-driven-development]] (read word by word), [[non-goals]], [[mob-spec-review]], [[three-amigos]], [[openspec]], [[keyset-pagination]] (sort whitelist, URL as source of truth).

*Tests & diagrams* — [[acceptance-test-bdd]] (pre-sorted data cheat, Playwright test agents), [[field-reality-diagram]] (zero-code OTel javaagent + Grafana LGTM), [[feedback-loop]], [[legacy-migration-stages]] (migrate first, build after; recipes over rewrites), [[openrewrite]].

*Review* — [[multi-model-review]] (another roll of the dice), [[code-review]], [[cloud-review-workflow]], [[ci-green-loop]], [[static-analysis-gauntlet]].

*Enterprise wiring* — [[model-context-protocol]], [[observability-mcp]] (rate-limited DB access), [[cli-vs-mcp-tradeoff]], [[rtk]], [[headroom]].

*Security* — [[supply-chain-attack]] (Shai-Hulud vs Axios, 3-day mirror age), [[prompt-injection]] (markdown-image exfil, logs, ClickFix), [[jailbreak-ladder]], [[lethal-trifecta]], [[docker-sandboxing]] (two-container build), [[agent-permissions]].

*Human factors* — [[ai-addiction]] (AI psychosis, 4-paddle ping-pong), [[agentic-engineering]] (12-hour runs), [[wispr-flow]] (`/voice`), [[skill-erosion]].

## See also
- [[2026-07-09-ai-live-casino]]
- [[2026-07-01-ai-java-community]]
- [[2026-08-03-ai-telecom-ops-qa]]
