---
title: JWT Identity
category: security
tags: [security, jwt, identity, system-prompt, prompt-injection]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

JWT identity is the pattern of extracting `ownerId`, name, and email from a validated JWT access token and injecting those claims into the [[system-prompt]] so the chatbot knows who it is serving.

## The pattern

The browser sends a JWT `Authorization: Bearer <token>` header with every request. The backend parses the token, extracts owner claims, and prepends them to the [[system-prompt]]: *"You are talking to Alice, owner id 42."* This survives a browser refresh and a fresh conversation — the bot knows the user without re-asking. See [JWT introduction](https://jwt.io/introduction).

## Two mandatory security steps

**1. Validate the signature.** A JWT is base64-encoded JSON — trivially readable and, without signature verification, trivially forgeable. Skip validation and you get **impersonation**: present someone else's token, the backend trusts it. Signed tokens cannot be silently tampered because the server holds the signing key.

**2. Never inject unvalidated text into the system prompt.** Even if the token is authentic, the `name` or `email` field might contain adversarial text crafted by the user at registration time — classic [[prompt-injection]] via a trusted channel. Sanitize claims before concatenation, or use structured placeholders.

## Demonstrated failure (live)

From [[2026-06-10-spring-ai-itkonekt]]: the demo skipped signature validation entirely. Result: impersonation was trivial, and attacker-controlled claim values concatenated directly into the [[system-prompt]] created a [[prompt-injection]] vector. Both consequences flow from the same root cause — treating unvalidated, user-reachable text as trusted server data.

## See also
- [[system-prompt]]
- [[prompt-injection]]
- [[tool-context]]
- [[tool-calling]]
- [[guardrails]]
- [[2026-06-10-spring-ai-itkonekt]]
