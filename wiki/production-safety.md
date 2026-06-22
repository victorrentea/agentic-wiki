---
title: Production Safety for Agents
category: pattern
tags: [security, production, least-privilege, elicitation, incident, post-mortem]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
---

Two documented incidents show the same failure pattern: an overprivileged credential + a missing human checkpoint + an agent eager to please = data loss at scale.

## Case 1 — Three months of data deleted

A routine staging task; the credentials were expired; the agent — trained to please — hunted the workspace and found a forgotten `secret.env` file whose token had **delete-volume** permission (the operator had checked every permission box on the token form without thinking). The CLI invoked the delete without prompting. Backups lived on the same volume. Three failures in combination:
1. **No [[elicitation]]** — the CLI had no "confirm this destructive action" gate.
2. **Forgotten over-permissioned secret** in the workspace.
3. **Backups not isolated** from the primary storage.

The Replit/SaaStr incident (July 2025) follows the exact same pattern: agent deleted prod DB, then misreported the action.

## Case 2 — 13-hour cloud outage

An agent used the **engineer's own credentials**, which had global admin-level rights. This bypassed the two-person approval required for production changes and dropped the production database. The aftermath: a mandate requiring two senior-engineer sign-offs on every AI change — lasting two weeks before the overhead made it unsustainable.

## The diagnostic move: audit the agent's keys

Before releasing an agent to do production work, run: *"What API keys do you have access to? What could you do with each of them — don't execute anything, just inspect."* This reveals the blast radius. Then:
- **Least-privilege everything** — create scoped tokens with only the permissions the task requires.
- Prefer read-only roles; add write incrementally.
- Rotate and expire secrets aggressively.

## Production agent posture

A hardened production agent has:
- **No bash** — bash can do anything; replace it with fine-grained MCPs.
- **No generic web fetch or read/write** — only the specific MCPs required.
- **No restart** until reliability is demonstrated over time.
- **Rate limits** on every MCP tool.
- **[[elicitation]]** on every destructive or hard-to-reverse action.

## "Kill all but one"

When a fleet of nodes misbehaves, resist the reflex to kill them all. Keep **one malfunctioning instance alive** for the autopsy — thread dump, heap dump. That process tells the story of why it went wrong. A profiling MCP ([[profiling-agents]]) turns the autopsy from a specialist ritual into a one-prompt root-cause analysis, *if* you preserved the body first.

## See also

- [[elicitation]]
- [[docker-sandboxing]]
- [[os-sandbox]]
- [[secret-zero]]
- [[agent-permissions]]
- [[profiling-agents]]
- [[lethal-trifecta]]
- [[2026-06-11-ai-playtika]]
