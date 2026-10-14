---
title: "ChatOps Agents: Bots That Do Work"
category: pattern
tags: [chatops, slack, teams, team-bot, automation, agent-auth, budget]
sources: ["[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

A ChatOps agent is an agent added as a member of the team's Slack/Teams channel that doesn't just answer questions but executes authorised work end-to-end — deploys, reports, lookups — on behalf of whoever asked.

## What it looks like

The "pro level" of team adoption ([[2026-06-02-ai-open-workshop]]): the bot sits in the team channel like a third colleague, with its own directory account. It deploys to staging, searches Jira, reads logs and spawns its own [[sub-agents]] — "collaborative prompting", one never-ending team conversation with the bot.

Examples seen in the field:

- **The BI team that evaporated.** A business request is posted in a channel. Claude authorises the sender (JWT), writes SQL against the data lake, builds the Excel report and e-mails it to the requester in 30–60 minutes, with the BI team out of the loop ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-31-ai-software-vendor]], [[2026-09-14-ai-industrial]]).
- **An ops bot in Teams** that provisions servers and flushes caches, with the requester's authorisation.
- **A "dependency check" bot** that answers "is endpoint X OK?" from **reality**, not from two-month-old docs: deployment manifests on git, the `aws` CLI for the load balancer, `kubectl` for pods. Build it in two steps: first a slash-[[agent-skill]], then the bot.
- The incident-response specialisation is the [[firefighter-agent]].
- **Off the shelf:** [Claude Tag](https://claude.com/docs/claude-tag/overview) is Anthropic's Slack version for Team/Enterprise plans ([[2026-09-04-ai-software-company]]).

A channel bot turns requests into **multiplayer prompting** — 2–3 people steering one Opus in the same thread ([[2026-08-31-ai-software-vendor]]).

## Three maturity levels

From [[2026-09-14-ai-industrial]] and [[2026-09-16-ai-safety-critical]]:

| Level | The bot… | Precondition |
|---|---|---|
| 1. Answers | answers questions from Jira/Confluence, logs, metrics | read-only credentials |
| 2. Acts on your behalf | runs the deploy, e-mails the report | authorises **who** asked before acting; allow-list + audit trail |
| 3. Proactive | quizzes the team on sprint decisions, or fixes what broke overnight | budget, tight tool scope, human review of its PRs |

Start read-only and add mutating actions only after an allow-list and an audit of who asked what.

## Design rules

- **Not a personal omnipotent bot.** Clear identity, its own budget, an audit trail and tightly limited tools ([[agent-auth]], [[agent-permissions]], [[production-safety]]).
- **The hard question is not technical: whose tokens does it burn?** Give it a shared team budget ([[token-economy]]).
- **Router + specialist for long-lived bots:** a cheap model routes every message, and an Opus sub-agent is launched per real interaction ([[model-hierarchy]]).
- **Wire systems once, through a gateway.** The state of the art is an LLM/MCP gateway (e.g. [Glean MCP Gateway](https://docs.glean.com/administration/platform/mcp/mcp-gateway)) connecting every internal system (Jira, Miro…) to all agents — see [[model-context-protocol]] and [[cli-vs-mcp-tradeoff]].
- **Support bots need an adversarial critic** — a second pass that checks the answer — so they don't lie to people ([[judge-llm]]).
- Identity must flow to the data: the bot queries with the requester's identity, so the data layer can refuse what that person may not see ([[jwt-identity]]).

## See also
- [[firefighter-agent]]
- [[agentic-corporation]]
- [[agent-auth]]
- [[sub-agents]]
- [[cli-vs-mcp-tradeoff]]
- [[jwt-identity]]
- [[model-hierarchy]]
- [[judge-llm]]
- [[ai-use-cases-beyond-coding]]
- [[2026-08-03-ai-telecom-ops-qa]]
- [[2026-09-14-ai-industrial]]
