---
title: "Source: Testing with AI @ a bank (2026-09-10..11)"
category: source
tags: [source, workshop, two-day, testing, qa, bank]
sources: []
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Two-day testing workshop (Romanian-language summary) for QA engineers and developers at a bank. Day 1 revisited why we test and what changes when tests are no longer hand-written: coverage as a misleading target, mocks that lie, API-level and integration tests, the testing honeycomb, mutation testing as the oracle to hand an agent, test data, and having the agent reproduce a bug with Playwright and write the red test. Day 2 put agents into a QA's daily work — reading the database through a read-only skill, driving the corporate browser profile, mining CI build history over REST, verifying data outputs against the ticket's formulas — with every struggle packaged as a skill pushed to an internal plugin marketplace, plus evals for agents and stubbing external systems.

## Concept inventory

*Verification instruments* — [[feedback-loop]] (build the instrument first, "done when the instrument is green"), [[mutation-testing]], [[agent-evals]], [[data-quality-validator]] (verify against the ticket's formulas), [[skills-benchmarking]].

*Test strategy* — [[acceptance-test-bdd]] (BDD's failed dream, API-level tests, honeycomb, Testcontainers/WireMock/stateful stubs, DB dumps), [[cognitive-debt]] (87% coverage with zero asserts), [[hardware-mock]], [[light-dark-code]] (the agent imitates the latest example test).

*Agent tooling for QA* — [[browser-mcp]] (corporate profile, `--user-data-dir`, Playwright test agents, ARIA-snapshot diffs), [[tool-calling]] (when NOT to use a tool, state-dependent exposure), [[ci-green-loop]] (Jenkins REST skill), [[agent-skill]] (lessons become skills, prove before publishing), [[plugin-marketplace]], [[openrewrite]].

*Context & cost* — [[token-economy]] (start-up overhead, subsidy maths, 1.3× measured productivity), [[model-hierarchy]], [[plan-mode]], [[sycophancy]] (echo chamber), [[hallucination]] (training-data poisoning), [[llm-wiki]] / [[cross-repo-knowledge]] (central memory).

*Safety & observability* — [[production-safety]] (read-only technical DB user), [[field-reality-diagram]] (trace ID end-to-end), [[wispr-flow]] (native dictation).

## See also
- [[2026-09-07-ai-bank]]
- [[mutation-testing]]
- [[acceptance-test-bdd]]
