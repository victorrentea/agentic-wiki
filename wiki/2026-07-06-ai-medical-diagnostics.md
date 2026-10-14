---
title: "Source: AI-Assisted Coding @ a medical-diagnostics company, Day 3 (2026-07-06 → 2026-07-08)"
category: source
tags: [source, medical-diagnostics, workshop, legacy, java-ee, qa]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

The deep hands-on day of a multi-day workshop, elbows-deep in a real legacy Java EE codebase (servlets, EJBs, Oracle, an Angular front-end). Less slideware, more scars: a live mob-review of a ≈100k-line byte-for-byte DAO→EJB refactor, a JDBC resource-leak hunt, a transaction-rollback flip caused by one interceptor annotation, regenerating a decoupled module from specs (strangler style), and a full QA track turning manual Word test-case documents into automated browser suites with rich reports. The recurring theme was "repaying the sins of the ancestors" — and turning every recurring bug into a scripted pipeline check instead of an eyeball hunt.

## Concept inventory

*Legacy & migration* — [[legacy-migration-stages]] ("move, don't rewrite", strangler regeneration from specs), [[openrewrite]] (script it, don't scan), [[code-graph]].

*Review* — [[code-review]] (preexisting bug vs PR regression, merge master backwards), [[static-analysis-gauntlet]] (every recurring bug → a pipeline check; check if the tool already ships the rule), [[multi-model-review]].

*QA & feedback loops* — [[test-case-doc-automation]], [[acceptance-test-bdd]] (per-suite data partitions), [[feedback-loop]], [[browser-mcp]], [[ai-use-cases-beyond-coding]], [[drift]] (single source of truth for authorization and reports).

*Human factors* — [[agentic-engineering]] (full adoption only, convergence = confidence, incremental rollout), [[skill-erosion]].

## See also
- [[2026-07-01-ai-java-community]]
- [[2026-07-03-ai-bank]]
- [[legacy-migration-stages]]
