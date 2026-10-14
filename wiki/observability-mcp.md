---
title: Observability MCP
category: tool
tags: [mcp, grafana, observability, pii, logs, metrics, anomaly-detection, database]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]"]
created: 2026-06-12
updated: 2026-10-13
---

Observability MCPs (Grafana, OpenSearch/Kibana) give agents read access to metrics, dashboards, logs, and traces — letting them derive production facts without a human copy-pasting numbers into the chat.

<span style="color:red">The sessions' slogan: *"Humans are not allowed to look at logs anymore"* ([[2026-07-01-ai-java-community]]). The corollary: AI is great at *reading* structured artifacts — logs, PromQL results, JFR, heap dumps, load-test reports, execution plans — and poor at *producing* them, so capture deterministically and interpret with AI ([[2026-05-06-ai-ecommerce]], [[2026-08-03-ai-telecom-ops-qa]], [[profiling-agents]]).</span>

## Grafana MCP

[Grafana MCP](https://github.com/grafana/mcp-grafana) lets an agent query dashboards, build new ones, and answer questions like *"what is the average execution time for this endpoint?"* without the operator knowing PromQL. It can even open the dashboard in the browser. The agent can also **expose business metrics** (e.g. `total_owners`) so that it learns production volumes itself — eliminating hand-fed facts and keeping estimates current. See [[observability-mcp#volumetry|Volumetry pattern]] below. <span style="color:red">Run it with `--disable-write` for read-only access; note that a Grafana MCP enabled for everyone is expensive in context ([[2026-09-02-ai-insurer]], [[dynamic-tool-discovery]]).</span>

<span style="color:red">**Trace navigator.** *"I'm looking at a log line in the code — what log appeared in any other system that called this, just before this log?"* The agent traverses the trace, jumps to the caller's logs and returns the previous entry: half-hour investigations in seconds. It **only works if the telemetry exists** — distributed tracing, central logs, metrics ([[2026-04-27-ai-healthcare-software]], [[telemetry-by-default]]).</span>

<span style="color:red">**Beyond dashboards**, an agent with an APM/Grafana/Datadog MCP can write **synthetic monitoring** smoke tests that run on schedule and fail loudly; report average/p99 per endpoint; list the **top-N endpoints by usage** as an instant load-shape map; spot anomalies over time; and compare **Core Web Vitals before/after a 1% canary** ([[2026-05-06-ai-ecommerce]]).</span>

## Database MCP

A [Postgres MCP](https://github.com/crystaldba/postgres-mcp) (and equivalents for other databases<span style="color:red">, e.g. [bytebase/dbhub](https://github.com/bytebase/dbhub)</span>) lets the agent query the DB, generate reports, and produce files (e.g. Excel). The access model must be **read-only role + hard statement timeout + read replica** — a read-only role cannot drop indexes but can still exhaust connections, CPU, or I/O into an effective DoS via pathological queries.

<span style="color:red">That DoS is not theoretical. Victor took down a pre-prod DB with an agent firing 10 parallel full-scan queries for 24 h ([[2026-07-20-ai-telecom]]); in another case the agent spawned experimental query variants and left bad ones running for hours, starving a shared Oracle instance ([[2026-06-02-ai-open-workshop]]). Hence:</span>

- <span style="color:red">**A dedicated, restricted, per-person read-only user — never your own credentials.** A cheap model with write rights near prod is how you get fired ([[2026-08-12-ai-bank]], [[2026-09-14-ai-industrial]]; [[agent-permissions]]).</span>
- <span style="color:red">**A replica, an anonymised mirror or a Docker copy — never production directly**; read-only queries can still scan billions of rows ([[2026-04-27-ai-healthcare-software]], [[2026-05-11-ai-software-vendor]]). Or a **local model that only sees the DB and emits data *patterns*** for your test-data factory, so real rows never reach a cloud model ([[2026-07-22-ai-agentic-how]]).</span>
- <span style="color:red">**Rate-limit it** — an argument for a DB MCP with rate limiting (or a `PreToolUse` hook throttling the calls, [[agent-lifecycle-hooks]]) over a raw CLI ([[2026-06-02-ai-open-workshop]], [[2026-07-20-ai-telecom]]).</span>
- <span style="color:red">**A DB parenting skill.** Asked to `EXPLAIN PLAN` and suggest an index, the agent proposed one that already existed because it hadn't read the schema → the skill says "never suggest schema changes without first reading the schema" ([[2026-06-08-ai-security-tools]]).</span>

<span style="color:red">**Make the schema AI-readable.** `COMMENT ON COLUMN … IS '…'` on non-obvious columns (`status = -1 ⇒ soft-deleted`) lets agents decode magic values — especially valuable with domain jargon or non-English column names. A large e-commerce company spent a year adding comments to every column, written for AI consumption; product people now ask in natural language and get SQL, a chart and an Excel back — hundreds-of-lines BI queries replaced by a sentence. Another lets staff query ≈600 tables in natural language ([[2026-04-27-ai-healthcare-software]], [[2026-05-25-ai-government]], [[2026-06-02-ai-open-workshop]], [[2026-09-14-ai-industrial]]).</span>

<span style="color:red">**Data democratisation.** *"Which owner has the most pets?"* → SQL from natural language → a chart or *"make an Excel for accounting"* → a formatted `.xlsx`; a manager builds dashboards by talking. The BI role shifts from query-writer to custodian of the data model ([[2026-05-11-ai-software-vendor]], [[2026-05-13-ai-it-services]], [[2026-05-04-ai-telecom]]). *"Data scientists no longer write SQL — they speak human language."*</span>

<span style="color:red">**Query tuning.** Models read execution plans better than most humans. A local Qwen driving Oracle through SQL\*Plus ran ≈40 queries, then handed two DBAs tuning ideas — 3 of 5 (in another telling, 3 of 4) they confirmed, some they hadn't found in months ([[2026-07-01-ai-java-community]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]]). More on [[profiling-agents]].</span>

## Log MCPs and anomaly detection

MCPs for OpenSearch/Kibana add two capabilities beyond raw log access:
- **Anomaly detection** — the agent can surface unusual log patterns automatically.
- **Log-pattern reverse-engineering** — compress repeated log lines into a pattern template + changing-parameter list ("you've logged this pattern 1000×; here are just the varying fields"), reducing token volume dramatically. <span style="color:red">OpenSearch ships this as its [log pattern tool](https://docs.opensearch.org/latest/ml-commons-plugin/agents-tools/tools/log-pattern-tool/) — the agent reads shapes, not values ([[2026-09-04-ai-software-company]]).</span>

## <span style="color:red">Never feed it raw logs</span>

<span style="color:red">The recurring horror story: an engineer pointed Opus at a multi-GB log ("investigate", "see if anything's wrong") and burned **≈$5–10k of quota in one day** in a compaction loop — after the model itself warned "boss, this is madness" ([[2026-05-06-ai-ecommerce]], [[2026-07-22-ai-agentic-how]], [[2026-09-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]]). Rules:</span>

- <span style="color:red">**Route it through a tool**, not 100k lines of grep: an Elasticsearch/Prometheus/Grafana MCP, or `grep`/`jq`/`tail` first and AI on the curated slice. Asked to analyse logs, Claude will often write its own Python anomaly scanner — the deterministic-script reflex, worth encouraging ([[2026-04-07-ai-product-team]], [[determinism-over-llm]]).</span>
- <span style="color:red">**Give leads**, not "look around": latencies, warnings, GDPR ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Use a cheap model on logs** — "you don't send the architect to shovel manure" ([[2026-09-14-ai-industrial]], [[model-hierarchy]]).</span>
- <span style="color:red">**Read-only SSH** for logs on servers: a non-root user with read access to the log folder only — otherwise the agent decides "it doesn't work, I'll kill it". It can still raise the log level without a restart while you reproduce (Spring Boot: `POST /actuator/loggers/<pkg>` with `configuredLevel: DEBUG`) ([[2026-09-16-ai-safety-critical]]).</span>

<span style="color:red">**Semantic log search across all company machines** is an ideal in-house vibe-coding project: a "search logs" skill plus SSH/SCP to the log servers; the agent builds its own `grep`/`sed`/`awk` to fish out only the slices it needs ([[2026-09-14-ai-industrial]], [[internal-tool-cli-skill]]).</span>

## <span style="color:red">What else to connect</span>

<span style="color:red">Beyond code: Jira/Confluence; the DB (read-only user on a replica); draw.io diagrams; logs and observability (mind PII — a metrics-only dashboard that exposes no logs is safe to grant); the QA team's test-case dashboard; meeting transcripts (local Whisper beats Teams' built-in quality) ([[2026-09-04-ai-software-company]]). [Sentry Session Replay](https://sentry.io/for/session-replay/) records the user's anonymised clicks, so a bug report arrives with a replay the agent can reproduce from; Sentry alerts are useful bug context too ([[2026-05-25-ai-government]], [[2026-06-08-ai-security-tools]]). A gateway such as [Glean](https://www.glean.com/platform/connectors) can front everything the company uses ([[2026-09-14-ai-industrial]]). Not everything needs an MCP: Kafka consumers are fine with a local library; business-flow orchestrators — read via MCP, write only through their own UI ([[2026-09-02-ai-insurer]], [[cli-vs-mcp-tradeoff]]). For static analysis, take the SonarQube CI report from its API (`report-task.txt` → `/api/qualitygates/project_status`, `/api/issues/search`), never by scraping ([[2026-08-03-ai-telecom-ops-qa]]).</span>

## PII risk

Logs, traces, and dashboards frequently contain personal data (player names, emails, transaction IDs). Giving the agent access to these sources is an **access review**, not a config toggle:
- Scrub / pseudonymize PII at ingestion (data minimization).
- Maintain a separate GDPR-safe role for the agent.
- The agent inherits whatever the dashboards expose — not what you *intended* to expose.
- <span style="color:red">**Hash in the MCP.** A DB MCP can mask responses with salted one-way hashing, so the same name still correlates across sources; red-team it monthly with a weak model ("can you deduce anything from what you see?"). Caveat: a fixed-salt hash of names is **pseudonymisation** ([GDPR art. 4(5)](https://gdpr-info.eu/art-4-gdpr/)), not anonymisation — the name dictionary is small and brute-forceable ([[2026-09-02-ai-insurer]]).</span>

<span style="color:red">Toward the vendor, enterprise plans can get [zero data retention](https://privacy.claude.com/en/articles/8956058-i-have-a-zero-data-retention-agreement-with-anthropic-what-data-is-retained) (on request, with Anthropic's approval); the bigger risk is a prompt-injected agent exfiltrating what it reads ([[data-governance]], [[lethal-trifecta]]).</span>

## Volumetry pattern

Expose a metric for any production fact you'd otherwise paste into the prompt (record count, request rate, p99 latency). Point the agent at Grafana and let it query the metric at analysis time. This keeps the agent's understanding current without hand-feeding, and anchors its design decisions (pagination thresholds, cache TTLs) to real data.

## <span style="color:red">Alarms designed at birth</span>

<span style="color:red">Shift observability left: while developing, the agent decides which metrics and dashboards to add to measure the CPU/latency delta of its change — "think from birth how you'll measure it". One breakout design: a skill reads the PR diff and emits (1) a JSON of ranked detections, weights and alert rules for an observability orchestrator and (2) a markdown for humans. T0 is the feature-flag activation; compare symmetric windows of 5/30/60 min, then "Thursday vs Thursday" (same hour, −7 days) against weekly patterns, and end with an explicit promote/rollback verdict — covering what a 10-minute deploy canary misses ([[2026-08-31-ai-software-vendor]]).</span>

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
- <span style="color:red">[[firefighter-agent]]</span>
- <span style="color:red">[[telemetry-by-default]]</span>
- <span style="color:red">[[agent-permissions]]</span>
- <span style="color:red">[[data-governance]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
