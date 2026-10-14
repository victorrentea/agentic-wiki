---
title: Field-Reality Diagram
category: pattern
tags: [observability, traces, plantuml, diagrams, drift, no-drift, opentelemetry]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-11
updated: 2026-10-13
---

A field-reality diagram is a sequence diagram *generated from real execution traces* rather than hand-drawn — so it shows the actual flow of a system and **cannot [[drift]]**, because it is regenerated on every run.

## The pipeline

Hand-drawn architecture diagrams rot the moment the code changes. The alternative, already working in practice: run e2e tests on staging at **100% trace sampling** → collect the traces in an observability backend (e.g. Grafana) → a small script renders them as a [PlantUML](https://plantuml.com) sequence diagram (e.g. published into Jira). The result is *the reality of the flow*, regenerated every run, so it stays truthful for free.

<span style="color:red">The concrete script: run the E2E suite against all services in Docker, collect [OpenTelemetry traces](https://opentelemetry.io/docs/concepts/signals/traces/) in Grafana, pull them through the Grafana API, and emit one PlantUML sequence diagram **named after the test** — browser → backend → services → DB → cloud, with SQL and parameters ([[2026-05-11-ai-software-vendor]], [[2026-09-30-ai-enterprise]]). "No LLM in the whole flow" ([[2026-07-23-ai-software-product]]). Because it's text, it stays editable by prompt. A working example lives in the [petclinic `genseq` tests](https://github.com/victorrentea/petclinic/tree/main/petclinic-backend/src/test/java/victor/training/petclinic/genseq).</span>

### Ways to capture the trace

| <span style="color:red">Mechanism</span> | <span style="color:red">Code change?</span> | <span style="color:red">Notes</span> |
|---|---|---|
| <span style="color:red">[OpenTelemetry Java agent](https://opentelemetry.io/docs/zero-code/java/agent/getting-started/) (`-javaagent`) + Grafana LGTM stack (Loki, Grafana, Tempo) in Docker</span> | <span style="color:red">none</span> | <span style="color:red">HTTP and DB calls come for free ([[2026-07-20-ai-telecom]]); [zero-code instrumentation](https://opentelemetry.io/docs/zero-code/) also exists for Python, PHP, Go and JavaScript ([[2026-09-04-ai-software-company]])</span> |
| <span style="color:red">[`@WithSpan`](https://opentelemetry.io/docs/zero-code/java/agent/annotations/) on critical methods</span> | <span style="color:red">annotations</span> | <span style="color:red">makes internal methods appear; can carry business metrics onto the diagram ([[2026-07-09-ai-live-casino]], [[2026-09-07-ai-bank]])</span> |
| <span style="color:red">**The browser starts the trace** during the E2E test</span> | <span style="color:red">test setup</span> | <span style="color:red">one trace ID from the click to the last microservice stitches the whole interaction ([[2026-07-01-ai-java-community]], [[2026-09-10-testing-ai-bank]])</span> |
| <span style="color:red">A Java agent recording who-calls-whom down to the JDBC driver</span> | <span style="color:red">none</span> | <span style="color:red">"there's no hallucination, it's the reality" ([[2026-07-03-ai-bank]])</span> |

<span style="color:red">Trace context must cross every hop: the [W3C `traceparent` header](https://www.w3.org/TR/trace-context/) over REST, and OpenTelemetry propagation through Kafka, RabbitMQ and other events ([supported libraries](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/main/docs/supported-libraries.md)) — that is what lets the diagram follow async flows ([[2026-07-30-ai-safety-critical]], [[2026-10-01-ai-bank]]). Elastic APM in Kibana also shows distributed traces, but Grafana was preferred for the trace → PlantUML flow.</span>

## What it catches

- <span style="color:red">**N+1 queries, visible to the naked eye.** A repeated `SELECT … WHERE owner_id=?` for one list call — *"for a single frontend call you made 21 queries"* ([Vlad Mihalcea on N+1](https://www.vladmihalcea.com/n-plus-1-query-problem/)). Give the picture to the agent and ask for one query. In one live case (owners + pets + visits serialized to show one name) the fix needed no code change: Hibernate [`default_batch_size`](https://docs.hibernate.org/orm/6.6/userguide/html_single/Hibernate_User_Guide.html#fetching-batch) ([[2026-08-31-ai-software-vendor]], [[2026-10-01-ai-bank]]). "Stupidity now has a picture."</span>
- <span style="color:red">**Race conditions between systems** — "the most damaging bugs are in the interactions between systems/modules". Teams found a scheduler-vs-websocket race and several WebSocket races by checking the arrows ("threads with loose ends") without reading code; one showed up as 13 calls instead of 12, another as 121 vs 122 ([[2026-07-01-ai-java-community]], [[2026-07-22-ai-agentic-how]], [[2026-08-13-ai-insurer]], [[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Cost:** one diagram revealed a missing cache hit; fixing it cut a hosting bill 10× ([[2026-07-23-ai-software-product]]).</span>
- <span style="color:red">**Broken observability — before the incident.** Missing traces mean OTel is misconfigured; if the chain breaks, somebody isn't propagating the trace header — that's *testing observability*, and it's a ticket ([[2026-05-11-ai-software-vendor]], [[2026-09-10-testing-ai-bank]], [[telemetry-by-default]]).</span>

## Practices

- <span style="color:red">**Commit the rendered diagrams to git after each run**, and review the **diff** of the diagram on each PR ([[2026-07-22-ai-agentic-how]], [[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Do it on the 1–2 critical flows out of 10** (auth, payments, websockets) — on all of them it's pain, cost and nerves ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">Add **toggles** to the generator (with/without SQL, with/without JSON bodies), or it eats the agent's tokens for nothing ([[2026-08-13-ai-insurer]]).</span>
- <span style="color:red">Sampling: 100% in e2e, ≈1% in prod, correlated by trace ID ([[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">On CI, Playwright `trace: 'retain-on-failure'` keeps browser traces only for failing tests; Playwright's [Trace Viewer](https://playwright.dev/docs/trace-viewer) time-travels every step, network included ([[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]]).</span>

<span style="color:red">*"The mature leap isn't 'AI writes documentation' — the system produces verifiable artifacts (OpenAPI, diagrams, traces, coverage, mutation reports) and the AI explains and maintains them."* ([[2026-06-08-ai-security-tools]], [[deterministic-diagrams]])</span>

## Why it beats both the model and the human

Because the diagram reflects what *actually happened*, an agent **can't cheat** against it: pointed at a trace and told "this PUT shouldn't trigger a websocket back", the AI couldn't bluff — it dug into the real behaviour and fixed it. This is the same anti-cheating value as reviewing [[acceptance-test-bdd|acceptance tests]] over mock tests, and the opposite philosophy to a `design.md` you **burn** because it drifts (see [[spec-driven-development]]). Inter-service sequence diagrams worth keeping in sync — for one-page incident triage — are exactly the diagrams to generate this way.

<span style="color:red">For a 30-microservice estate the recorded trace report *is* the input you hand the agent ("which systems does my flow traverse?"), not a hallucinated one ([[2026-09-04-ai-software-company]]).</span>

## From sequences to architecture diagrams

<span style="color:red">Aggregate the per-test sequences into a [[c4-model]] **C2 container diagram** — the level hardest to keep in sync with what is deployed ([[2026-04-07-ai-product-team]], [[2026-09-28-ai-gaming-tech]]). That aggregate **validates the draw.io diagram in Confluence**: keep the hand-drawn diagram, but test it against the traces so **an unexpected edge fails the build** ([[2026-09-30-ai-enterprise]], [[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Before a breaking change, find your consumers three ways: telemetry traces (or trace IDs printed in logs); the service accounts permitted to call you; and a **scream test** — safe only behind feature flags or blue-green. Banks add **shadow deployment**: the same input goes to old and new, outputs are compared, writes suppressed ([playbook](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/shadow-testing/); see [[record-replay-refactoring]]) ([[2026-10-09-ai-jax-london]]).</span>

The *static-structure* cousin of this runtime pattern is [[archunit-drift-control]]: reflect the class diagram out of the compiled code (and unit-test package dependencies with ArchUnit) so a stale `.puml` fails the build. Both make drift structurally impossible by regenerating from the source of truth rather than maintaining a parallel artifact by hand.

## Companion checks

- <span style="color:red">**Visual regression** proves only the intended ≈5 screens changed (Playwright `toHaveScreenshot()`) — see [[review-by-images]] ([[2026-09-30-ai-enterprise]]).</span>
- <span style="color:red">A **syntactic** scan (syntax tree, not text find) of log statements newly added in a diff flags GDPR-relevant fields: `vetId` is fine; `user.name`, phone numbers or ticket comments are not ([[2026-09-07-ai-bank]], [[data-governance]]).</span>

## See also
- [[drift]]
- [[acceptance-test-bdd]]
- [[spec-driven-development]]
- [[profiling-agents]]
- [[archunit-drift-control]]
- <span style="color:red">[[c4-model]]</span>
- <span style="color:red">[[deterministic-diagrams]]</span>
- <span style="color:red">[[telemetry-by-default]]</span>
- <span style="color:red">[[observability-mcp]]</span>
- <span style="color:red">[[record-replay-refactoring]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
