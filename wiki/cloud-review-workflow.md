---
title: Cloud Review Workflow
category: pattern
tags: [code-review, cloud-agents, pr-bot, three-amigos, spec-driven, automation]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-12
updated: 2026-06-24
---

The cloud review workflow is a "no laptop" pipeline where every step from ticket to merged PR happens in the cloud — triggered by a Jira ticket, driven by bots in cloud runners, reviewed from a phone.

## The pipeline

1. **Inputs** — Jira/Linear ticket + team chat + meeting notes + email. Wire them directly so the bot can read them — never re-tell or copy-paste.
2. **Business vibes the UX.** For UI-heavy work, the business drafts a rough version (vibe-codes it) and hands it to a developer who becomes the **[[vibe-fixer]]** — "you have a dream, I clean your shit." The developer's leverage is now judgement: what won't scale, what to reuse, what to throw away.
3. **[[three-amigos]] session.** Business + developer + tester (the "ninja") machine-gun the ticket until acceptance tests (Gherkin) emerge.
4. **PR auto-created from the ticket.** A **cloud bot self-activates**, reads the ticket + acceptance tests, and runs a dedicated **research phase** (read-only, no writing) to understand the codebase. Research time is non-negotiable — rushing it is the most common cause of wrong implementation.
5. **Bot builds the SDD** (proposal / design / tasks). It tags the team on the PR when ready.
6. **Review by role** — architect reviews `design.md`, product reviews `proposal.md`, tester reviews acceptance criteria. Everyone drops PR comments from wherever they are; corrections flow back via PR comments ("don't type the fix — maybe you're wrong").
7. **`tasks.md` broken down for a cheaper model.** The tasks file exists so a Sonnet-class model can execute without the full reasoning load; humans barely review it.
8. **Or just let Opus + swarm run it.** For tasks smaller than a sprint, the orchestrator holds the plan in its head and spins sub-agents per sub-chapter on the fly. Load tests fit here too.

**Policeman agent** — when many PRs run in parallel overnight, a [[policeman-agent|policeman agent]] blocks a new task from starting if its expected file-set overlaps an open PR, preventing merge collisions. A **selective second reviewer** is triggered only for genuinely risky files (Gradle/build files, swagger definitions, shared utilities).

## The "no laptop" property

Everything runs in cloud runners (analogous to GitHub Actions / `@claude-on-PR`). The engineer steers entirely from PR comments — no local checkout needed. This also contains blast radius: the bot cannot reach the developer's local secrets, SSH keys, or home folder.

## Why "weakest link" framing

Code ships faster than review scales: a 4-day task drops to 3, but you're not shipping 3× more because you're reviewing more. The cloud workflow is a *desperate optimization of code review* — not a requirement for every feature, but the structured response to "we are the bottleneck."

## See also

- [[three-amigos]]
- [[vibe-fixer]]
- [[spec-driven-development]]
- [[multi-model-review]]
- [[code-review]]
- [[acceptance-test-bdd]]
- [[sub-agents]]
- [[policeman-agent]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
