---
title: "Source: AI-Assisted Coding @ agentic.how (2026-06-26)"
category: source
tags: [source, agentic-how, workshop, one-day, remote]
sources: []
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

One-day remote workshop (≈25 participants, Zoom) distilling Victor Rentea's flagship two-day *Agentic Engineering* course into a single-day intensive. The session centred on two hand-drawn diagrams — **Conversation Context** (anatomy of what enters the LLM window) and **Agentic Workflow** (the full delivery pipeline from requirements to reviewed code) — with everything in between being craft: CLAUDE.md discipline, MCP vs CLI, spec-driven development, multi-agent review, security sandboxing, and protecting human skill.

## Concept inventory

*Foundations* — [[harness]] vs [[model-hierarchy]], [[context-window]] anatomy, [[dumb-zone]], [[prompt-caching]] 5-min TTL, [[elicitation]] as MCP's killer feature, [[cli-vs-mcp-tradeoff]].

*Knowledge engineering* — [[claude-md]] on Git, [[agents-md-discipline]] (anger-driven growth, [[decision-context]] over ADRs, [[self-improving-knowledge]] from JSONL), [[cross-repo-knowledge]] (green-team-ai-central symlink), skill vs [[rag]] vs [[llm-wiki]] tripartite, [[skill-marketplace-security]].

*Spec-driven workflow* — [[grill-me]] (one question at a time; [[grilling-in-reverse]] for code comprehension), [[spec-driven-development]] (proposal → design → tasks → acceptance tests), [[acceptance-test-bdd]] immutable feature files, [[non-goals]], self-proving loops, [[tasks-md-parallelism]].

*Review pipeline* — [[multi-model-review]] (Codex CLI cross-model adversarial), [[code-review]] (CI watch hook, "+1500 lines = war"), [[codeowners-elders]] (CI workflows, OpenAPI, domain model, pom.xml, diagrams), [[archunit-drift-control]] + [[bug-heatmap]] to prioritize cleanup.

*Security* — [[docker-sandboxing]] gold standard, keys on host, [[prompt-injection-canary]], red-teaming the sandbox, [[supply-chain-attack]] extending to [[skill-marketplace-security]].

*Human factors* — [[skill-erosion]] ("yeah okay Claude"), [[cognitive-debt]] parallel-agent dumb zone, [[grilling-in-reverse]], [[sprint-design-quiz]].

## See also
- [[2026-06-23-ai-garmin]]
- [[2026-06-22-ai-kambi]]
- [[llm-wiki]]
