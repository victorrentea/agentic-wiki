---
title: Agent Authentication
category: concept
tags: [security, auth, service-account, bearer, on-behalf-of, mcp, non-human-identity, rfc-8693]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-12
updated: 2026-10-13
---

An agent acting on behalf of a human user needs two credentials simultaneously: a **service-account API key** that identifies the application, and a **bearer token** that carries the human user's identity.

## Two-credential model

When an agent drives an application through [[model-context-protocol]], two identities must travel together:

1. **Service-account API key** — machine-to-machine credential; answers "which application is this?" and authorizes the bot against the backing service.
2. **On-behalf-of bearer token** — the human user's identity (typically a JWT from the same IDP the app uses); answers "as whom is this agent acting?" and enforces per-user authorization and audit trails.

This split ensures that audit logs record *which human* performed an action, not just *which bot*, and that per-user authorization rules (rate limits, data scoping, approvals) remain effective even when an agent drives the interaction.

<span style="color:red">In an `.mcp.json` the shape is: **JWT = the human, API key = the specialist agent** ([example in petclinic](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)) [[2026-09-28-ai-gaming-tech]].</span>

## Non-human identity: authorize the agent itself

<span style="color:red">The two-credential model is a stepping stone. By autumn 2026 the recommendation is that the server must **authorize the agent itself** — which model, which tools, for which purpose — not just the human behind it [[2026-09-30-ai-enterprise]].</span>

- <span style="color:red">**One identity per agent.** Give every agent its **own API key and credentials**, never your full human identity — otherwise, after an incident, "which of my 40 agents did the boo-boo?" has no answer. Register specialist agents as [non-human identities](https://owasp.org/www-project-non-human-identities-top-10/2025/top-10-2025/) in the authorization system [[2026-09-04-ai-software-company]].</span>
- <span style="color:red">**On-behalf-of metadata on every call.** The ideal is a **single expiring JWT belonging to the agent, with an "on behalf of" claim** — standardised by [RFC 8693 token exchange](https://datatracker.ietf.org/doc/html/rfc8693) (`sub` = the human, [`act`](https://datatracker.ietf.org/doc/html/rfc8693#name-act-actor-claim) = the agent), so the post-mortem names both agent and human [[2026-09-04-ai-software-company]] [[2026-09-30-ai-enterprise]].</span>
- <span style="color:red">**Grant and revoke tools per role.** A [[firefighter-agent]] reads production logs and the DB, but gets no Bash, no Write, no internet — it only reports to stdout. See [[agent-permissions]].</span>
- <span style="color:red">**A local CLI can't do this**: `gh` or `az` carry only *your* token, so an agent driving them is indistinguishable from you — see [[authenticated-cli]].</span>

## Anonymous agent → authenticated agent

<span style="color:red">A banking-chatbot pattern uses **two agents**: an anonymous one that only converses; after login the conversation switches to a second agent whose MCP servers carry the user's bearer token — new conversation, different rights [[2026-08-03-ai-telecom-ops-qa]]. That authenticated chatbot, whose MCPs run on the user's JWT, is the real exploitation target for [[prompt-injection]] and the [[lethal-trifecta]] [[2026-08-06-ai-bank]].</span>

## Where identity must NOT live

Never pass user identity as a [[tool-calling]] argument (model-authored data) — the model can be talked into forging it. Identity must travel **out-of-band**, typically as an HTTP header on the [[mcp-transport]] request. See [[tool-context]] and [[jwt-identity]] for the validation side.

## Your login is the agent's blast radius

<span style="color:red">**A CLI login gives the agent your full rights**: after `az login` it could delete every ticket you can delete. A safer pattern is a **throwaway session** that only exports the needed tickets to markdown and is then killed; the long-running agent works from the export [[2026-09-24-ai-bank]].</span>

<span style="color:red">"Give the token once; the agent stores it under your home folder" (an `.env.example` in the skill documents the variable) is convenient but sits in tension with [[dont-run-in-home-folder]]: such credentials must stay outside the agent's working tree, ideally behind an [[os-sandbox]] or [[secret-zero]] [[2026-09-24-ai-bank]].</span>

<span style="color:red">Keep coding agents away from production entirely (in one team, the production DB was reachable only through [Azure Bastion](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview)). A **production data agent** gets only: no internet, one output folder, read-only access to a replica — better still, iterate on staging and let *a human* run the result in prod. See [[production-safety]] [[2026-09-24-ai-bank]].</span>

## Headless agent pattern

When using `claude -p` (headless / non-interactive mode) to drive multi-turn user interactions, the same credential split applies: the CLI session holds the service-account key, and the user's bearer token is injected per-turn via headers or tool context — not pasted into the conversation as text.

<span style="color:red">Demo: a Sonnet support agent with **only MCP tools, no Bash** ([support-chatbot.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/support-chatbot.md)), driven by `claude --resume -p` as the chatbot harness — don't rebuild that loop yourself. See [[headless-claude]] [[2026-09-28-ai-gaming-tech]].</span>

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[jwt-identity]]
- [[tool-context]]
- [[tool-calling]]
- [[headless-claude]]
- <span style="color:red">[[authenticated-cli]]</span>
- <span style="color:red">[[agent-permissions]]</span>
- <span style="color:red">[[firefighter-agent]]</span>
- <span style="color:red">[[production-safety]]</span>
- <span style="color:red">[[secret-zero]]</span>
- <span style="color:red">[[lethal-trifecta]]</span>
- [[2026-06-11-ai-mobile-gaming]]
