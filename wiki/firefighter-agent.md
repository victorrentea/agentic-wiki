---
title: Firefighter / On-Call Agent
category: pattern
tags: [production, incident-recovery, orchestrator, observability, human-in-the-loop, state-machine]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-07-01-ai-java-community]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-22
updated: 2026-10-13
---

A firefighter agent is an on-call incident-recovery agent that gathers diagnostic intelligence before a human even connects — cutting mean-time-to-recover to minutes by doing the slow, mechanical work (log triage, metric correlation, root-cause hypothesis) autonomously, then handing a human the button for any mutating action.

## What it does before the human wakes up

When an alert fires, the agent:
1. Correlates logs, DB query plans, [[observability-mcp|Grafana metrics]], and distributed traces.
2. Forms a root-cause hypothesis and lists candidate fixes (invalidate cache, replay/drop a Kafka message, scale up instances, restart a node).
3. Presents a ranked action plan to the on-call engineer.
4. **Waits for human approval** before any mutating action.

In observed deployments, the on-call team *wakes up just to approve what the agent already diagnosed*, not to do the detective work.

<span style="color:red">**Field data (2026).** Five of Victor's client companies run a production firefighter. At one large software company, by the time humans arrive **more than half the incidents are already solved** (restart, clear cache, drain Kafka), and the bots greet engineers with *"I noticed X, tried Y, please approve this PR."* Another team reported MTTR "down to ≈30%" after adding a production agent with no internet, few allowed moves and escalation to humans. One team's agent investigates an error on the production log tail within ≈2 seconds ([agent definition demo](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/firefighter.md.demo)); another **builds a custom dashboard for each incident**, so the on-call lands on a pre-investigated overview. A more ambitious claim — ≈90% of pages pre-mitigated, MTTR in seconds — was reported in one session but is unverified.</span>

## <span style="color:red">Two roles: Sherlock and Firefighter</span>

<span style="color:red">Split the job. A **"Bug Sherlock"** investigates logs, metrics, traces and a production replica and proposes a root cause **without acting** — it can hold a broad read-only token. A **Firefighter** on stand-by tries a few bounded remediations: before the human connects it works through ≈5 ideas within its permissions (tweak a parameter ±5%, restart one machine, a read-only prod query), then waits with a root-cause hypothesis, buttons to approve, and a ticket filed for the post-mortem.</span>

<span style="color:red">A support-ticket agent needs exactly **three accesses** — the places you go with the ticket in your arms: **logs** ("the king" of incident investigation), **code**, and a **browser** to reproduce the bug where the user complains ([[browser-mcp]]). If your step 1 is Splunk, so is its. Without a **trace-id attached to the ticket** it is blind — tools like [Sentry session replay](https://sentry.io/product/session-replay/) or [OpenReplay](https://openreplay.com) ship the bug report with a ring buffer of the last minutes (user gestures, requests, headers); see [[telemetry-by-default]]. Train it on the team's **post-mortems**.</span>

## Capability scope — carefully restricted

The agent holds environment-scoped tokens:
- **Read** access: logs, DB read replica, Grafana, traces — unlimited.
- **Write** access scoped to non-production environments (dev/test Argo CD, staging restarts).
- **Mutating prod actions** (cache invalidation, Kafka replay, pod restart) require explicit human approval via an approval gate before execution.

This is the same [[agent-permissions]] spectrum applied per environment: the agent earns wider permissions in prod gradually, as reliability is demonstrated.

<span style="color:red">**Rollout: start with nothing and open one hole per week.** Begin **read-only for a quarter** (dashboards, correlated-log retrieval); then two tools plus a "call the human" tool; then graduate to controlled remediations (restart with cooldown) once trusted. Red-team it in staging — *"try to destroy this replica"* (the hard part is convincing Opus to do harm).</span>

## <span style="color:red">Guardrails live in the tools, not the prompt</span>

<span style="color:red">*"You can ask AI 'please don't restart more than twice' — it doesn't work. Lock it up so it can't do harm."* The posture is **not `kubectl` with a powerful token, but narrow MCPs with the operating rules codified** inside them (security by tooling, as in [[mcp-tenant-isolation]]):</span>

- <span style="color:red">**No raw DB MCP.** Expose prepared queries only; wrap each in a tool with a hard timeout (e.g. 500 ms) and a rate limit (e.g. 1 query/s); read-only by default, scoped per table or even per row. Deep investigations run on a **read-only backup** where it can dig freely.</span>
- <span style="color:red">**Numeric bounds.** Max 2 restarts per instance per incident ("you may not do a third restart of the same node within the same incident"), at most half the instances restarted, one cache eviction, config changes only between fixed values, a budget beyond which it stops and pages a human.</span>
- <span style="color:red">**Temporary authorisation** per task type or incident zone, not permanent rights. The on-call key is read-only; write credentials are taken ad hoc (break-glass). The agent gets its own non-personal identity. An all-powerful internal token (e.g. a CI server's) with only an expiry date is a reason to send **Opus, not Haiku** — the stronger model is less likely to do something stupid with it ([[model-hierarchy]]).</span>
- <span style="color:red">**Kill it past ≈500k tokens — no compaction.** One team's ops rule: Opus, effort high, 1M context, and a hard kill once the context passes ≈500k, because a long incident session degrades ([[dumb-zone]]).</span>
- <span style="color:red">**No internet** for the production agent, and remediation scripts tested on preprod (e.g. 5 instances) before prod.</span>

## <span style="color:red">The state machine: a chess player</span>

<span style="color:red">Model the production agent as a **chess player**: from each state it has a fixed set of legal moves; you design the board, it does the reasoning. A [LangGraph](https://docs.langchain.com/oss/python/langgraph/overview) state machine might define 7 allowed prod actions with restricted transitions — after "restart pod", max 3 retries, then a different path. Typical moves in increasing severity: evict cache → replay Kafka → restart node. The AI *chooses* the transition, but only from the bounded set: *"it gives you strategy, it doesn't leave you in an infinite loop."* Same design space as [[embabel]]'s goal-oriented planning.</span>

## Security posture

- Environment-scoped tokens (dev token ≠ prod token), never a single omniscient key.
- An **audit trail** on every action attempted and every approval requested.
- [[draft-only-email]] principle extended to ops: agent prepares the Slack/PagerDuty message, human sends.
- No auto-rollback in prod without a human gate — rollback can itself cause data loss.
- <span style="color:red">**Logs are untrusted input.** A malicious payload in a log line can steer an agent that reads logs — a [[prompt-injection]] channel straight into the agent with the most dangerous tools.</span>
- <span style="color:red">**One agent per environment at a time.** Two parallel agent demos on the same environment trampled each other's data.</span>
- <span style="color:red">Production-deletion stories — an all-scopes token in a forgotten `secret.env`, prod, staging and backups on one volume — are collected in [[production-safety]].</span>

## <span style="color:red">From firefighter to team bot</span>

<span style="color:red">Late-stage adoption puts a bot in the team chat acting with the *asker's* identity ([Claude in Slack](https://code.claude.com/docs/en/slack)): it answers from Confluence/Jira, supports **mob prompting** (five humans, one bot), and greets the team in the morning with a PR fixing last night's e2e failure. See [[chatops-agent]] and [[agentic-corporation]].</span>

## Why it works

The bottleneck in incident response is usually the detective work (30–60 minutes of log-reading) not the execution (5-minute restart). Offloading the detective work to an agent that can ingest gigabytes of logs in seconds compresses MTTR dramatically. The human stays "the button" — the one who decides, with full situational awareness assembled by the agent.

## See also
- [[observability-mcp]]
- [[agent-permissions]]
- [[docker-sandboxing]]
- [[draft-only-email]]
- [[sub-agents]]
- [[production-safety]]
- <span style="color:red">[[telemetry-by-default]]</span>
- <span style="color:red">[[mcp-tenant-isolation]]</span>
- <span style="color:red">[[embabel]]</span>
- <span style="color:red">[[chatops-agent]]</span>
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[browser-mcp]]</span>
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-04-27-ai-healthcare-software]]</span>
- <span style="color:red">[[2026-05-11-ai-software-vendor]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
