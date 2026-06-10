---
title: Tool Calling
category: security
tags: [tools, security, parameter-hardening, spring-ai, agentic]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Tool calling is the mechanism by which an LLM invokes developer-defined functions with parameters it invents — making those parameter values an adversarial trust boundary.

## The core hazard

The LLM **invents** every tool parameter value. It does not read them from a trusted store; it generates them from context it has been given (or manipulated into). From [[2026-06-10-spring-ai-itkonekt]]: a `sendEmail` tool was live-demonstrated as a spam cannon — talk the bot into changing the recipient address, then instruct it to *"send 100 emails numbered 1–100"* and it obediently does. See [Spring AI tool calling reference](https://docs.spring.io/spring-ai/reference/api/tools.html).

The tools you define are **the place of your control.** Treat every model-authored parameter as untrusted user input — validate, constrain, and authorize server-side. The model's stated "intent" is not authorization.

## Hardening parameters

**Regex + throw.** Add a regex guard on sensitive parameters (e.g., email subject must match `[TICKET-\d+] …`). If it fails, **throw an exception back into the agent**. The agent, eager to please, will iterate: it invents `ticket 1`, you tighten the pattern, it invents `TICKET-1`. This is still insufficient — it only shapes format.

**Existence validation.** The real fix: verify the invented value actually exists in an authoritative source (Jira ticket exists, recipient is in the org directory). Then the agent physically cannot proceed without a real artifact — hallucinated values fail validation and the tool refuses.

## Sensitive identity

Never let the model supply the caller's identity as a tool argument. Pass it [[tool-context|out-of-band]] via [[tool-context]] so it cannot be forged. See also [[jwt-identity]] for the JWT-based identity injection pattern and [[prompt-injection]] for how unvalidated identity becomes an injection vector.

## See also
- [[tool-context]]
- [[jwt-identity]]
- [[prompt-injection]]
- [[guardrails]]
- [[mcp-transport]]
- [[2026-06-10-spring-ai-itkonekt]]
