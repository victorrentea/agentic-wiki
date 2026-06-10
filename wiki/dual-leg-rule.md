---
title: Dual-Leg Rule
category: security
tags: [security, agent-design, data-exfiltration, least-privilege, lethal-trifecta]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

The dual-leg rule is Victor's practical form of Simon Willison's **[lethal trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)**: an agent must never simultaneously hold access to confidential data AND the ability to send or receive data externally — cut one of those legs.

## The lethal trifecta

[Simon Willison's lethal trifecta](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/) names three capabilities that are catastrophic *in combination*: (1) access to private data, (2) exposure to untrusted content (the [[prompt-injection]] vector), and (3) the ability to communicate externally. Hold all three and a single injected instruction reads your confidential data through one leg and exfiltrates it through another — no extra vulnerability needed; the capability combination *is* the vulnerability.

Victor's "cut one leg" framing collapses the untrusted-input and external-output legs into a single *external send/receive* capability and gives the actionable rule: never pair it with confidential-data access.

## The rule, stated plainly

From [[2026-06-10-spring-ai-itkonekt]]: *"An agent with access to confidential data must not also have the ability to send/receive externally — and vice versa. Cut one leg."*

This is an application of least-privilege, but the framing matters: it is not about limiting what the agent *should* do, it is about making exfiltration **architecturally impossible** regardless of what the model is talked into.

## Payment example

A practical architecture: an **outer orchestrator agent** has the full user context and card data but zero network reach beyond the internal service bus. It drives a tiny **inner "payer" agent** that can only reach the payment gateway — and that inner agent never sees card numbers. The gateway call is the only external leg; the confidential data never touches it.

This pattern composes: each specialized sub-agent gets exactly one leg, and orchestration logic lives in the agent that has neither sensitive data nor external reach — it only routes.

## See also
- [[prompt-injection]]
- [[tool-calling]]
- [[tool-context]]
- [[supply-chain-attack]]
- [[mcp-transport]]
- [[elicitation]]
- [[2026-06-10-spring-ai-itkonekt]]
