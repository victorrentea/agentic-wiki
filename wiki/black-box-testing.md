---
title: Black-Box Tests at the Service Seam
category: pattern
tags: [testing, test-strategy, component-tests, mocks, fixtures, wiremock, testcontainers, coverage]
sources: ["[[2026-04-07-ai-product-team]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

The test strategy for agent-written code: put most of the tests that matter **black-box at the boundary of one service or module** — real HTTP in, real DB, external systems faked on the wire — so the agent can rewrite the interior freely while the tests that humans read stay stable.

## Why the seam moved

*"What was prohibitively expensive to write — Cucumber, step definitions, component tests — is now cheap. That's why the calculus changes"* [[2026-08-06-ai-bank]] [[2026-06-08-ai-security-tools]]. Class-level, mock-heavy unit tests break on every refactor and cost a reviewer ≈3,000 lines per PR [[2026-05-11-ai-software-vendor]]; black-box tests at the service edge survive any rewrite — *"if I rewrite it in Go tomorrow, the tests must stay green"* [[2026-08-03-ai-telecom-ops-qa]]. Functional integration tests (REST → DB with Testcontainers; Spring Boot's `@ServiceConnection` starts containers from an annotation) freeze *system* behaviour so the AI can refactor internals [[2026-04-07-ai-product-team]].

Taken to the limit, confidence in black-box tests makes **"flush the service and regenerate it with the next model"** a rational plan — e.g. 10,000 Python tests guarding a Java service [[2026-08-10-ai-consultancy]]; one port-logistics manager was ready to regenerate the whole codebase with each new model as long as the acceptance tests stayed green [[2026-05-11-ai-software-vendor]]. **Black-box tests are the precondition for a big refactor**: fear of refactoring is a lack of tests, not a lack of hexagonal architecture — escalate every unit test to a system test (REST on the left, WireMock on the right, Kafka in Testcontainers) with no coverage drop, and sell it politically as the preparatory step [[2026-08-03-ai-telecom-ops-qa]].

## The shape: honeycomb, not pyramid

The [testing honeycomb](https://engineering.atspotify.com/2018/01/testing-of-microservices) puts integration tests in the middle, unit tests only where logic is genuinely hard, and few E2E tests because they're expensive (142 E2E tests ran for an hour) [[2026-09-10-testing-ai-bank]]. See also [Fowler on test shapes](https://martinfowler.com/articles/2021-test-shapes.html) [[2026-05-11-ai-software-vendor]].

| Layer | Value for agents | Rule |
|---|---|---|
| Unit (a method, 1–2 classes) | a cheap **micro feedback loop for the AI**; "never had non-regression value" | don't review; keep only for dense logic |
| **Component / service** ([Fowler](https://martinfowler.com/articles/microservice-testing/#testing-component-introduction)) | maps to requirements, stable, high signal/noise | **≈80% of the tests**, WireMock + Testcontainers allowed, mocks not |
| Contract ([Pact](https://docs.pact.io/), Spring Cloud Contract, versioned schemas) | keeps the *surface* in sync across services | not stateful logic |
| E2E | the happy path; slow and flaky | parallelise hard, keep few |

Sources: [[2026-06-08-ai-security-tools]] [[2026-08-10-ai-consultancy]] [[2026-08-13-ai-insurer]] [[2026-09-02-ai-insurer]].

- **"Unit tests are dying"** was the most controversial claim of one day: per Kent Beck a *unit* is a unit of behaviour, not a class ([Fowler, UnitTest](https://martinfowler.com/bliki/UnitTest.html)). Keep fine-grained unit tests where there is dense business logic — validations, calculations, a class with ≈529 branches in ≈2,300 lines — not on boring classes [[2026-05-11-ai-software-vendor]] [[2026-06-08-ai-security-tools]]. Extract a 40-branch algorithm into a near-static pure function and table-test it; full black-box coverage of it is combinatorially impossible [[2026-08-10-ai-consultancy]].
- **Unit tests written by AI have no value for the human, but they do for the AI** — a short loop so it doesn't burn tokens on late failures, especially on weak models [[2026-08-27-ai-it-services]]; on Opus their guard value is "shady" [[2026-08-31-ai-software-vendor]]. Re-check that assumption at every model upgrade [[2026-08-10-ai-consultancy]].
- **Test at API level, like seniors — not with `@Mock`, like juniors.** A method without logic is covered by passing *through* it from the API test; implementation-detail classes (a validator behind a service) need no tests of their own ([prefer testing public APIs](https://testing.googleblog.com/search/label/TotT)) [[2026-09-10-testing-ai-bank]]. Well-sliced services expose ≈95% of behaviour via the API [[2026-09-28-ai-gaming-tech]]. Instead of a unit test on a heavy class, ask for a **component test on behaviour** [[2026-09-30-ai-enterprise]].
- **Test wide, not narrow**: the wider the test, the more freedom the agent has underneath. Don't re-test what the framework already tests (e.g. Spring's `Pageable`) [[2026-06-02-ai-open-workshop]].

## Mocks lie

- "7 mocks returning mocks" are worthless [[2026-06-08-ai-security-tools]]; *"a mock means you can no longer explain the test to a human"* [[2026-08-27-ai-it-services]]. Mocks couple the test to an **AI-invented micro-design that changes tomorrow** ([Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)) [[2026-09-16-ai-safety-critical]]; "Generate tests" even mocks the mapper [[2026-09-30-ai-enterprise]].
- Test A without B and the glue between them breaks unseen — a WireMock between two microservices stays green after B is split in two [[2026-09-10-testing-ai-bank]].
- Review **[sociable tests](https://martinfowler.com/bliki/UnitTest.html)** (no mocks between classes), not `@Mock`-heavy ones [[2026-10-06-gray-factory-devoxx]].

**Mock the wire, not the method**: let the code make a real HTTP request to a fake server — [WireMock](https://wiremock.org/), [MockServer](https://www.mock-server.com/) [[2026-06-08-ai-security-tools]]. External-system doubles in order of fidelity [[2026-09-10-testing-ai-bank]]:

1. [Testcontainers](https://testcontainers.com/) running the **exact production image**;
2. [WireMock record/playback](https://wiremock.org/docs/record-playback/) of real traffic — re-record one real request a week to catch contract changes;
3. a hand-written **stateful stub** (a hash map plus a `__reset` endpoint between tests) — the generalisation of [[hardware-mock]].

Not `@SpringBootTest`, ideally: deploy in Docker, call the API, Testcontainers + WireMock around. **Choosing the seam** — is the DB inside the box? the cache? grey-box DB reads are "kind of cheating" — is a strategic decision; the supreme criterion is that the test maps onto a requirement and reads "crystal clear" to the PO [[2026-08-31-ai-software-vendor]].

## Which tests get read

- **"Critical" = crosses your system's boundary** — integrations, money, high RPS, public shame: those tests get read. **"Complex"** = cyclomatic/cognitive complexity ≈ the minimum number of tests; ask the agent for the **sum per entry point (REST/Kafka) across the call graph**, not per method [[2026-08-13-ai-insurer]].
- **Test language is chosen by who reviews**: business doesn't look → the project language; business/QA read → JUnit `@DisplayName` + `@Nested` (the run tree reads as sentences: "create customer should… fail if… city too short"); business works with you → Gherkin with Examples tables ([[acceptance-test-bdd]]) [[2026-08-13-ai-insurer]]. Test DSLs in TypeScript are hard for non-devs to read — try a snake_case DSL [[2026-09-02-ai-insurer]].
- **No human sees a failing test before an agent has investigated it** — you validate the report, not the log [[2026-08-13-ai-insurer]].

## Coverage targets

- You don't need 85% — that was a whip to make people write tests; **≈60% on critical paths** [[2026-07-09-ai-live-casino]]. Another view moves the target to **acceptance tests at ≈80%**, "not 100% as a religion"; coverage from mock-heavy tests nobody reads means nothing, and characterization tests written only to enable a legacy refactor don't count [[2026-09-07-ai-bank]].
- ≈80% of real coverage comes from component tests, although Sonar is usually configured to measure only unit tests [[2026-08-13-ai-insurer]].
- **Coverage was never the goal**: AI hits 100% trivially, and tests nobody reads just get regenerated [[2026-09-28-ai-gaming-tech]]. Count only the tests a human actually read — harvest the PR tool's **"Viewed" checkbox** as proof [[2026-10-06-gray-factory-devoxx]]. Requirement-level coverage is on [[semantic-coverage]].
- **Prove a test tests something**: change one line of production code yourself (rename a field, off-by-one, delete the sorting) and run the tests — if they still pass, they were lying. Automated, that is [[mutation-testing]] [[2026-05-13-ai-it-services]] [[2026-08-27-ai-it-services]] [[2026-09-10-testing-ai-bank]].

## Fixtures as a tree

A complicated feature is a **tree**: a common trunk (mandatory setup), pivot points, edge-case branches, leaves. Today's representation — 200 lines of test with one line added mid-way — never shows the map or "which floor you're on" [[2026-09-02-ai-insurer]].

- **Operationalised**: collapse the common trunk of slow suites into a loadable **DB dump per "floor"** and fire every test from there; Postgres [`CREATE DATABASE … TEMPLATE`](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) or a Testcontainers snapshot restores in ≈1 s. Name the fixtures ([ObjectMother](https://martinfowler.com/bliki/ObjectMother.html)) so the team shares a language [[2026-09-04-ai-software-company]].
- **One fixture per business state** — loan requested / quoted / approved; credit file pre-approved / approved / executed — behind one button: reset DB, insert fixture, start clean ([green.sql](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/resources/db/fixtures/green.sql)) [[2026-09-28-ai-gaming-tech]] [[2026-09-30-ai-enterprise]].
- When restoring dumps, mask per-run values (timestamps, UUIDs) before comparing; resetting sequences kills parallelism [[2026-09-10-testing-ai-bank]].
- A two-page setup is only worth it if dozens of tests branch from it [[2026-09-16-ai-safety-critical]]. Mock-free journeys can insert data directly in the DB and number steps 60, 70 so you can insert 65 later [[2026-08-06-ai-bank]].

**Known start state + parallelisation are the thorniest automation problems** [[2026-09-30-ai-enterprise]].

## Speed and flakiness

- **Partition data per suite** (a fresh user/tenant/device, cleaned up after) so parallel runs don't race — 100 invented tenants = 100 simultaneous tests [[2026-07-06-ai-medical-diagnostics]] [[2026-08-13-ai-insurer]]. E2E tests mostly wait on other systems — parallelise until the CPU pins [[2026-08-10-ai-consultancy]].
- **Flaky = a race**: parallelise and fix it; "retry N times" is a workaround. Poll, don't sleep; cap each test at <2 min [[2026-07-23-ai-software-product]].
- 20 near-identical tests sharing 95% of their steps is an acceptable DRY violation — it mirrors production's blast radius; when a shared change breaks all 20, hand the failures to an agent and review only the fix [[2026-08-10-ai-consultancy]].
- Profile which setup step eats the time; gate on a smoke test of the core flow; run likely-failing tests first ([Predictive Test Selection](https://docs.gradle.com/develocity/predictive-test-selection/), commercial); [shard](https://playwright.dev/docs/test-sharding) a 100-minute suite across VMs [[2026-09-10-testing-ai-bank]].
- Demote trivia (max length, validation messages) to fast UI-unit tests, tagged as such in the report [[2026-07-06-ai-medical-diagnostics]].
- **Nightly chaos**: fill the nightly pipeline with every test type and inject network faults ([Toxiproxy](https://github.com/Shopify/toxiproxy): delay/drop packets, close sockets, throttle bandwidth) — "if the power bill didn't rise by morning, you're doing it wrong" [[2026-09-16-ai-safety-critical]].

## See also

- [[acceptance-test-bdd]]
- [[feedback-loop]]
- [[mutation-testing]]
- [[semantic-coverage]]
- [[hardware-mock]]
- [[openapi-test-fixtures]]
- [[red-green-tdd]]
- [[reward-hacking]]
- [[cognitive-debt]]
