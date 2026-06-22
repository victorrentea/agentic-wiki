---
title: Firefighter / On-Call Agent
category: pattern
tags: [production, incident-recovery, orchestrator, observability, human-in-the-loop]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-22
new_in: 2026-06-22
---

<span style="color:red">🆕 New in AI@Kambi (2026-06-22)</span>

A firefighter agent is an on-call incident-recovery agent that gathers diagnostic intelligence before a human even connects — cutting mean-time-to-recover to minutes by doing the slow, mechanical work (log triage, metric correlation, root-cause hypothesis) autonomously, then handing a human the button for any mutating action.

## What it does before the human wakes up

When an alert fires, the agent:
1. Correlates logs, DB query plans, [[observability-mcp|Grafana metrics]], and distributed traces.
2. Forms a root-cause hypothesis and lists candidate fixes (invalidate cache, replay/drop a Kafka message, scale up instances, restart a node).
3. Presents a ranked action plan to the on-call engineer.
4. **Waits for human approval** before any mutating action.

In observed deployments, the on-call team *wakes up just to approve what the agent already diagnosed*, not to do the detective work.

## Capability scope — carefully restricted

The agent holds environment-scoped tokens:
- **Read** access: logs, DB read replica, Grafana, traces — unlimited.
- **Write** access scoped to non-production environments (dev/test Argo CD, staging restarts).
- **Mutating prod actions** (cache invalidation, Kafka replay, pod restart) require explicit human approval via an approval gate before execution.

This is the same [[agent-permissions]] spectrum applied per environment: the agent earns wider permissions in prod gradually, as reliability is demonstrated.

## Security posture

- Environment-scoped tokens (dev token ≠ prod token), never a single omniscient key.
- An **audit trail** on every action attempted and every approval requested.
- [[draft-only-email]] principle extended to ops: agent prepares the Slack/PagerDuty message, human sends.
- No auto-rollback in prod without a human gate — rollback can itself cause data loss.

## Why it works

The bottleneck in incident response is usually the detective work (30–60 minutes of log-reading) not the execution (5-minute restart). Offloading the detective work to an agent that can ingest gigabytes of logs in seconds compresses MTTR dramatically. The human stays "the button" — the one who decides, with full situational awareness assembled by the agent.

## See also
- [[observability-mcp]]
- [[agent-permissions]]
- [[docker-sandboxing]]
- [[draft-only-email]]
- [[sub-agents]]
- [[production-safety]]
- [[2026-06-22-ai-kambi]]
