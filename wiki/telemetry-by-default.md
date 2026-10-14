---
title: Telemetry by Default (OpenTelemetry + RED)
category: pattern
tags: [observability, opentelemetry, grafana, tracing, red-method, feedback-loop]
sources: ["[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-06-08-ai-security-tools]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Telemetry by default is the rule that every project ships with zero-code telemetry from day one, so that both humans and agents can observe the running system — *"there's no excuse in 2026 not to have telemetry on the project."*

## Why agents need it

An agent that can only read code guesses about runtime behaviour; an agent that can query traces, metrics and logs can *see* it. Telemetry is the runtime half of the [[feedback-loop]]: it lets the [[firefighter-agent]] investigate an incident, a [[profiling-agents|profiling agent]] find the slow span, and an [[observability-mcp]] answer "what happened at 14:02?" with data instead of inference.

## Instrumentation is free

- **Zero code.** The [OpenTelemetry Java agent](https://opentelemetry.io/docs/zero-code/java/agent/) attaches with one `-javaagent` flag and instruments HTTP, JDBC, messaging and common frameworks without touching the source. Other ecosystems have equivalent auto-instrumentation.
- **One header everywhere.** Every language propagates the same W3C `traceparent` header, so a trace survives hops across services, languages and clouds.
- **Local stack in minutes.** The **LGTM** stack — Loki (logs), Grafana, Tempo (traces), Mimir (metrics) — runs on a laptop under Colima or Podman, so developers and agents get the same view locally as in production.

## Dashboards: the RED method

Build service dashboards around the [RED method](https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/) — **Rate** (requests per second), **Errors** (failed requests), **Duration** (latency distribution; "Duration", not "Delay"). Three panels per endpoint answer most "is it healthy?" questions.

Agent prompt of the day: *"Scan the project, build a Grafana dashboard on the risk points, generate test traffic, and open it in the browser."* The agent does the tedious wiring; you review whether the chosen risk points make sense.

## Trace the AI too

Propagate `traceparent` all the way into MCP tools and the LLM server, and demand it from your model provider — otherwise the slowest hop of an AI feature is a black box in the trace ([[chatbot-latency-tuning]]).

## Traces as test output

E2E tests can capture the distributed trace of each scenario. Collected per test, those traces show which components a business flow really touches — the input for a [[field-reality-diagram]] that compares the drawn architecture with what actually runs.

## See also
- [[observability-mcp]]
- [[firefighter-agent]]
- [[profiling-agents]]
- [[feedback-loop]]
- [[field-reality-diagram]]
- [[chatbot-latency-tuning]]
- [[2026-08-03-ai-telecom-ops-qa]]
