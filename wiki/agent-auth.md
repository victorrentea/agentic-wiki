---
title: Agent Authentication
category: concept
tags: [security, auth, service-account, bearer, on-behalf-of, mcp]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-12
updated: 2026-06-12
new_in: 2026-06-12
---

<span style="color:red">🆕 New in AI@Playtika Day 2 (2026-06-12)</span>

An agent acting on behalf of a human user needs two credentials simultaneously: a **service-account API key** that identifies the application, and a **bearer token** that carries the human user's identity.

## Two-credential model

When an agent drives an application through [[model-context-protocol]], two identities must travel together:

1. **Service-account API key** — machine-to-machine credential; answers "which application is this?" and authorizes the bot against the backing service.
2. **On-behalf-of bearer token** — the human user's identity (typically a JWT from the same IDP the app uses); answers "as whom is this agent acting?" and enforces per-user authorization and audit trails.

This split ensures that audit logs record *which human* performed an action, not just *which bot*, and that per-user authorization rules (rate limits, data scoping, approvals) remain effective even when an agent drives the interaction.

## Where identity must NOT live

Never pass user identity as a [[tool-calling]] argument (model-authored data) — the model can be talked into forging it. Identity must travel **out-of-band**, typically as an HTTP header on the [[mcp-transport]] request. See [[tool-context]] and [[jwt-identity]] for the validation side.

## Headless agent pattern

When using `claude -p` (headless / non-interactive mode) to drive multi-turn user interactions, the same credential split applies: the CLI session holds the service-account key, and the user's bearer token is injected per-turn via headers or tool context — not pasted into the conversation as text.

## See also

- [[model-context-protocol]]
- [[mcp-transport]]
- [[jwt-identity]]
- [[tool-context]]
- [[tool-calling]]
- [[headless-claude]]
- [[2026-06-11-ai-playtika]]
