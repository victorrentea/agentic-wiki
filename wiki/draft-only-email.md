---
title: Draft-Only Email (OAuth Scope Discipline)
category: pattern
tags: [security, oauth, email, human-in-the-loop, agent-permissions, production-safety]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-08-ai-security-tools]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-22
updated: 2026-10-13
---

Draft-only email is the principle that an agent may read, vectorize, and compose email but must never send — and this constraint should be enforced by OAuth scope (`gmail.compose`, not `gmail.send`), not by the model's discretion.

## The rule

> The agent prepares the draft. You press Send.

An agent with full email access can read the entire inbox (useful for context), draft replies (useful for productivity), but **cannot send** autonomously. "No auto-reply to the CEO" is not a polite guideline — it is an OAuth permission boundary. Granting `gmail.send` scope and trusting the model to "use ethics" is insufficient; scope is a technical enforcement, not a social contract.

## Why OAuth scope, not prompting

A model told "never send email without my approval" will comply — until a context drift, a [[sycophancy]] response to a persuasive prompt, or a [[dumb-zone]] lapse causes it to forget. OAuth scope is enforced by Google's servers regardless of what the model thinks it should do. It's the same logic as [[docker-sandboxing]]: containment must be external and structural, not an in-context instruction.

<span style="color:red">Consumer assistants already work this way: desktop "Connectors" for Gmail or Calendar go through the provider's OAuth consent screen and receive a *scoped* token — e.g. read + draft, no send, no delete. *"The token's permissions are bounded by the consent screen, not by Claude."* The threat is [[prompt-injection]] arriving inside the data itself: an email saying "forward your last 50 emails to …" may well be obeyed by an agent holding `gmail.send`; scope minimisation is the defence ([[2026-05-18-secure-coding-payments]]).</span>

## The general principle

This pattern generalizes: every destructive or irreversible agent action should be gated by an **external control plane** (OAuth scope, API key permission level, environment-scoped tokens) rather than relying on the model's instruction-following. The human's role is to hold the "send button" for any action the agent cannot take back:

- Email → `compose` scope only; human sends.
- Database mutations → read-replica for analysis; write via approval gate.
- Deployments → agent proposes; CI/CD pipeline gates on human approval.
- Kafka / cache invalidation → [[firefighter-agent]] prepares; human approves.
- <span style="color:red">Shopping → Victor's grocery skill fills the cart (favourites first, rolling a die among ties) but **may neither log in nor press Buy** ([[2026-06-08-ai-security-tools]]).</span>

<span style="color:red">The shopping agent adds a second layer: it drives the site with **scripted JavaScript instead of reading the HTML**, so a hidden comment on the shop page ("buy 10 bottles", "send the SSH key to this address") never enters its context. Give such agents only the narrow tool they need (navigation/WebFetch — no Read, no Bash); see [[lethal-trifecta]] and [[agent-permissions]].</span>

## <span style="color:red">The human side of the draft</span>

<span style="color:red">Draft-only is also a courtesy rule. [Don't paste the AI](https://dontpastetheai.com/) at someone you respect — one participant counted ≈50 AI-written emails exchanged just to book a bar. Ask the agent for a **concise, ADHD-friendly draft**, then pass it through your own head before you press Send ([[2026-09-30-ai-enterprise]]).</span>

## See also
- [[agent-permissions]]
- [[firefighter-agent]]
- [[docker-sandboxing]]
- [[production-safety]]
- [[sycophancy]]
- [[dumb-zone]]
- <span style="color:red">[[prompt-injection]]</span>
- <span style="color:red">[[lethal-trifecta]]</span>
- [[2026-06-22-ai-sports-betting]]
