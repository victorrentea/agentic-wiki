---
title: Acceptance Tests & BDD
category: pattern
tags: [testing, bdd, playwright, karate, spec-driven, review, gherkin]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-24
---


Acceptance tests express behaviour as plain-English given/when/then scenarios (with assertions in the **`then`** step) so they constrain the agent from the *outside* while a [[spec-driven-development|spec]] constrains it from within.

## Squeeze the AI from both sides

The strategy is to squeeze the chaotic agent between (a) the **design decisions** you anticipated and (b) the **acceptance tests** that must go green. The prompt pattern: *"turn the first two scenarios into [Playwright](https://playwright.dev) feature files — don't implement backing code yet — then implement until they're green."* That is behaviour-driven development, and business and testers love it: plain English, no JSON, no null. For API-level or game clients, [Karate](https://github.com/karatelabs/karate) does given/when/then at the REST level.

## Review acceptance tests, not mock tests

The load-bearing insight: **AI cheats on unit tests** — not by deleting assertions, but by **programming the mock to return exactly what its own implementation produces**. Reviewing 700 lines of mock-coupled unit tests against 500 lines of implementation is "the most worthless activity for a human today." So **review the acceptance tests, and review the code** — let the model's TDD-generated unit tests exist, but don't spend your attention on them. This is the testing face of [[multi-model-review]] and the [[hallucination]] cause of sycophancy ("all tests pass!" — the ones it didn't delete).

**Business-reviewed `.feature` files are the real safety net** — not unreviewed coverage numbers. The collapse story: a team hits 85%+ coverage but never reviews the tests while the AI rewrites them alongside the code, so the net is fake (see [[cognitive-debt]]). Plain-English Gherkin `.feature` files the *business* can read are the layer that actually catches wrong behaviour. A practical `AGENTS.md` rule makes AI-TDD honest: *"after writing tests, summarise in plain English what each test asserts and wait for human approval before implementing"* — a human reviews the **test contracts** (signatures + asserts), not necessarily every value.

The independence constraint is critical: QA (or a human) must lock the `.feature` files *before* implementation starts. If the agent generates both the Gherkin and the implementation in the same session, the acceptance tests confirm only what the agent already decided to build — they are not an independent oracle.

## Run them isolated

Drive acceptance tests in a sandbox (e.g. backend + DB + frontend in Docker, headless) — "machines with guns there; it leaves my environment." This is the same containment principle as the [[ralph-loop]]'s Docker sandbox.

## See also
- [[spec-driven-development]]
- [[multi-model-review]]
- [[hallucination]]
- [[field-reality-diagram]]
- [[ralph-loop]]
- [[cognitive-debt]]
- [[agents-md-discipline]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
