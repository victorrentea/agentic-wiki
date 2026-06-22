---
title: Draft-Only Email (OAuth Scope Discipline)
category: pattern
tags: [security, oauth, email, human-in-the-loop, agent-permissions, production-safety]
sources: ["[[2026-06-22-ai-kambi]]"]
created: 2026-06-22
updated: 2026-06-22
new_in: 2026-06-22
---

<span style="color:red">🆕 New in AI@Kambi (2026-06-22)</span>

Draft-only email is the principle that an agent may read, vectorize, and compose email but must never send — and this constraint should be enforced by OAuth scope (`gmail.compose`, not `gmail.send`), not by the model's discretion.

## The rule

> The agent prepares the draft. You press Send.

An agent with full email access can read the entire inbox (useful for context), draft replies (useful for productivity), but **cannot send** autonomously. "No auto-reply to the CEO" is not a polite guideline — it is an OAuth permission boundary. Granting `gmail.send` scope and trusting the model to "use ethics" is insufficient; scope is a technical enforcement, not a social contract.

## Why OAuth scope, not prompting

A model told "never send email without my approval" will comply — until a context drift, a [[sycophancy]] response to a persuasive prompt, or a [[dumb-zone]] lapse causes it to forget. OAuth scope is enforced by Google's servers regardless of what the model thinks it should do. It's the same logic as [[docker-sandboxing]]: containment must be external and structural, not an in-context instruction.

## The general principle

This pattern generalizes: every destructive or irreversible agent action should be gated by an **external control plane** (OAuth scope, API key permission level, environment-scoped tokens) rather than relying on the model's instruction-following. The human's role is to hold the "send button" for any action the agent cannot take back:

- Email → `compose` scope only; human sends.
- Database mutations → read-replica for analysis; write via approval gate.
- Deployments → agent proposes; CI/CD pipeline gates on human approval.
- Kafka / cache invalidation → [[firefighter-agent]] prepares; human approves.

## See also
- [[agent-permissions]]
- [[firefighter-agent]]
- [[docker-sandboxing]]
- [[production-safety]]
- [[sycophancy]]
- [[dumb-zone]]
- [[2026-06-22-ai-kambi]]
