---
title: Tool Calling
category: security
tags: [tools, security, parameter-hardening, spring-ai, agentic]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-09-10-testing-ai-bank]]"]
created: 2026-06-10
updated: 2026-10-13
---

Tool calling is the mechanism by which an LLM invokes developer-defined functions with parameters it invents — making those parameter values an adversarial trust boundary.

## The core hazard

The LLM **invents** every tool parameter value. It does not read them from a trusted store; it generates them from context it has been given (or manipulated into). From [[2026-06-10-spring-ai-open-workshop]]: a `sendEmail` tool was live-demonstrated as a spam cannon — talk the bot into changing the recipient address, then instruct it to *"send 100 emails numbered 1–100"* and it obediently does. See [Spring AI tool calling reference](https://docs.spring.io/spring-ai/reference/api/tools.html).

The tools you define are **the place of your control.** Treat every model-authored parameter as untrusted user input — validate, constrain, and authorize server-side. The model's stated "intent" is not authorization.

## Hardening parameters

**Regex + throw.** Add a regex guard on sensitive parameters (e.g., email subject must match `[TICKET-\d+] …`). If it fails, **throw an exception back into the agent**. The agent, eager to please, will iterate: it invents `ticket 1`, you tighten the pattern, it invents `TICKET-1`. This is still insufficient — it only shapes format.

**Existence validation.** The real fix: verify the invented value actually exists in an authoritative source (Jira ticket exists, recipient is in the org directory). Then the agent physically cannot proceed without a real artifact — hallucinated values fail validation and the tool refuses.

## <span style="color:red">Describing and exposing tools</span>

<span style="color:red">**Say when NOT to use it.** Each tool description should state its exclusions too, otherwise the agent confuses similar tools. Write it as if for yourself in 3–4 months ([example MCP server in petclinic](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java)). See [[model-context-protocol]].</span>

<span style="color:red">**State-dependent exposure.** Control which tools the agent may call at each step according to the state it has reached: first offer two tools; once it picked "the hammer", expose a single option. Fewer choices per step means fewer wrong calls and less [[tool-poisoning]] surface — the same idea as [[dynamic-tool-discovery]].</span>

## Sensitive identity

Never let the model supply the caller's identity as a tool argument. Pass it [[tool-context|out-of-band]] via [[tool-context]] so it cannot be forged. See also [[jwt-identity]] for the JWT-based identity injection pattern and [[prompt-injection]] for how unvalidated identity becomes an injection vector. <span style="color:red">The same rule scales to multi-tenant MCP servers: [[mcp-tenant-isolation]].</span>

## See also
- [[tool-context]]
- [[jwt-identity]]
- [[prompt-injection]]
- [[guardrails]]
- [[mcp-transport]]
- [[model-context-protocol]]
- [[dynamic-tool-discovery]]
- [[mcp-tenant-isolation]]
- [[2026-06-10-spring-ai-open-workshop]]
