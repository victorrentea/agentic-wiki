---
title: Observability MCP
category: tool
tags: [mcp, grafana, observability, pii, logs, metrics, anomaly-detection]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-12
updated: 2026-06-25
---

Observability MCPs (Grafana, OpenSearch/Kibana) give agents read access to metrics, dashboards, logs, and traces — letting them derive production facts without a human copy-pasting numbers into the chat.

## Grafana MCP

[Grafana MCP](https://github.com/grafana/mcp-grafana) lets an agent query dashboards, build new ones, and answer questions like *"what is the average execution time for this endpoint?"* without the operator knowing PromQL. It can even open the dashboard in the browser. The agent can also **expose business metrics** (e.g. `total_owners`) so that it learns production volumes itself — eliminating hand-fed facts and keeping estimates current. See [[observability-mcp#volumetry|Volumetry pattern]] below.

## Database MCP

A [Postgres MCP](https://github.com/crystaldba/postgres-mcp) (and equivalents for other databases) lets the agent query the DB, generate reports, and produce files (e.g. Excel). The access model must be **read-only role + hard statement timeout + read replica** — a read-only role cannot drop indexes but can still exhaust connections, CPU, or I/O into an effective DoS via pathological queries.

## Log MCPs and anomaly detection

MCPs for OpenSearch/Kibana add two capabilities beyond raw log access:
- **Anomaly detection** — the agent can surface unusual log patterns automatically.
- **Log-pattern reverse-engineering** — compress repeated log lines into a pattern template + changing-parameter list ("you've logged this pattern 1000×; here are just the varying fields"), reducing token volume dramatically.

## PII risk

Logs, traces, and dashboards frequently contain personal data (player names, emails, transaction IDs). Giving the agent access to these sources is an **access review**, not a config toggle:
- Scrub / pseudonymize PII at ingestion (data minimization).
- Maintain a separate GDPR-safe role for the agent.
- The agent inherits whatever the dashboards expose — not what you *intended* to expose.

## Volumetry pattern

Expose a metric for any production fact you'd otherwise paste into the prompt (record count, request rate, p99 latency). Point the agent at Grafana and let it query the metric at analysis time. This keeps the agent's understanding current without hand-feeding, and anchors its design decisions (pagination thresholds, cache TTLs) to real data.

## Latency metric for incremental pipelines

For data pipelines, emit a metric for the **latency between a row appearing in production** (its timestamp) and **landing in the output table**. Measure this before optimizing a slow incremental merge. This is the observability prerequisite for [[optimize-then-measure-subset]]: you can't responsibly parallelize what you haven't measured.

**Data-quality validator companion:** rather than having an agent eyeball code for data-quality conformance, build a programmatic validator — feed known input, assert the output conforms to the schema/standards. See [[data-quality-validator]].

## See also

- [[model-context-protocol]]
- [[cli-vs-mcp-tradeoff]]
- [[mcp-sandbox-inheritance]]
- [[profiling-agents]]
- [[token-economy]]
- [[optimize-then-measure-subset]]
- [[data-quality-validator]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
