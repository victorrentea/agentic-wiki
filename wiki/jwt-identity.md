---
title: JWT Identity
category: security
tags: [security, jwt, identity, system-prompt, prompt-injection]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-18-secure-coding-payments]]"]
created: 2026-06-10
updated: 2026-10-13
---

JWT identity is the pattern of extracting `ownerId`, name, and email from a validated JWT access token and injecting those claims into the [[system-prompt]] so the chatbot knows who it is serving.

## The pattern

The browser sends a JWT `Authorization: Bearer <token>` header with every request. The backend parses the token, extracts owner claims, and prepends them to the [[system-prompt]]: *"You are talking to Alice, owner id 42."* This survives a browser refresh and a fresh conversation — the bot knows the user without re-asking. See [JWT introduction](https://jwt.io/introduction).

## Two mandatory security steps

**1. Validate the signature.** A JWT is base64-encoded JSON — trivially readable and, without signature verification, trivially forgeable. Skip validation and you get **impersonation**: present someone else's token, the backend trusts it. Signed tokens cannot be silently tampered because the server holds the signing key.

**2. Never inject unvalidated text into the system prompt.** Even if the token is authentic, the `name` or `email` field might contain adversarial text crafted by the user at registration time — classic [[prompt-injection]] via a trusted channel. Sanitize claims before concatenation, or use structured placeholders.

<span style="color:red">**3. Even a clean name in the prompt is the wrong place for authorization.** The LLM is stateless; deep in a dense context it may simply *forget* which user it is serving and answer as someone else. Identity in the system prompt is fine for tone ("Hi Alice"); the access decision belongs in the tool — see below ([[2026-05-11-ai-software-vendor]]).</span>

## <span style="color:red">Authorization in the tool, not the prompt</span>

<span style="color:red">The stronger pattern passes the JWT to the tools, never to the model. The MCP tool receives the token as a header, validates it and touches only that user's rows (`… WHERE customer_id = jwt.sub`); the model never sees the token, so it cannot leak or corrupt it ([[tool-context]], [[agent-auth]]).</span>

<span style="color:red">**JWT-isolated sub-conversation.** Run it in two phases: an anonymous conversation with **no tools** → the user authenticates → a *fresh* conversation whose MCP tools are bound to that user's JWT, with each backend enforcing row-level security against it. Even a fully jailbroken model can then reach only one user's data — the fix for the confused-deputy problem, and the same family as [[mcp-tenant-isolation]] ([[2026-05-06-ai-ecommerce]], [[2026-05-18-secure-coding-payments]]).</span>

<span style="color:red">**Gotcha:** any tool that caches across invocations (connection pools, result caches) must put the JWT/user in the cache key, or users cross-contaminate.</span>

<span style="color:red">**Why per-tool authz matters for internal agents too.** In a public court case, a fired developer's logic bomb (`IsDLEnabledinAD`) locked thousands of colleagues out the moment his Active Directory account was disabled — a four-year sentence in March 2025 ([CSO Online](https://www.csoonline.com/article/4044730/disgruntled-developer-gets-four-year-sentence-for-revenge-attack-on-employers-network.html)). An internal agent holding broad privileges can do the same lateral damage unless each tool checks who it is acting for ([[agent-permissions]]).</span>

## Demonstrated failure (live)

From [[2026-06-10-spring-ai-open-workshop]]: the demo skipped signature validation entirely. Result: impersonation was trivial, and attacker-controlled claim values concatenated directly into the [[system-prompt]] created a [[prompt-injection]] vector. Both consequences flow from the same root cause — treating unvalidated, user-reachable text as trusted server data.

## See also
- [[system-prompt]]
- [[prompt-injection]]
- [[tool-context]]
- [[tool-calling]]
- [[guardrails]]
- <span style="color:red">[[mcp-tenant-isolation]]</span>
- <span style="color:red">[[agent-auth]]</span>
- <span style="color:red">[[agent-permissions]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
