---
title: Judge LLM
category: security
tags: [security, guardrails, classifier, llm-as-judge, adversarial]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-08-ai-security-tools]]", "[[2026-09-02-ai-insurer]]"]
created: 2026-06-10
updated: 2026-10-13
---

A judge LLM is a separate, cheap classifier model that screens both the incoming user prompt and the outgoing response for safety and topicality before the reply reaches the user.

## How it works

The judge runs independently of the main chat model. It receives the message under review and answers two binary questions: *Is this legitimate? Is it on-topic?* If either answer is no, it early-returns a refusal and logs the event into [[chat-history]] — the main model never runs (or its response is suppressed). This is the `JudgeGuard` pattern demonstrated in [[2026-06-10-spring-ai-open-workshop]].

**Why it works — two complementary reasons:**

1. **Clear mind.** The judge has no conversation context; it reviews with fresh eyes, making it harder to drift via multi-turn manipulation (see [[prompt-injection]]).
2. **Dice twice.** An attacker must beat both models independently. Even if the main model is fooled, the judge is a second, uncorrelated checkpoint.

## Model choice

Use a fast, cheap turbo/mini model as judge — never your main model. Cost and latency are the only real downsides of this pattern. For stronger coverage, run **different model families** as judges, and split concerns: one judge for safety, another for legal, another for topicality. Correlated judges from the same family share the same blind spots.

Real-world deployments confirm the pattern is mainstream: ChatGPT runs two judges; Anthropic's [[tool-calling]] infrastructure uses a strong model as judge on high-risk tool output (e.g., WebFetch over untrusted websites).

<span style="color:red">Harness-side content filters — e.g. a coding assistant's "refuse to write malware" filter — are judges sitting between the user and the main model: if they flag the exchange, the main model never sees the prompt, or its answer is replaced. The typical choice is a small fast model (Haiku- or mini-class) ([[2026-05-18-secure-coding-payments]]).</span>

## Layering a public chatbot

<span style="color:red">A judge is one layer in a stack. The order recommended for a public chatbot ([[2026-09-02-ai-insurer]]):</span>

1. <span style="color:red">[[system-prompt]] with **examples and counter-examples** of on- and off-topic requests ([[example-driven-prompting]]);</span>
2. <span style="color:red">a regex/flag on suspicious words — cheap and trivially bypassed, but it catches the lazy attempts;</span>
3. <span style="color:red">an **input gate** LLM returning an on-topic **confidence 0..1**, with a threshold instead of a yes/no;</span>
4. <span style="color:red">an **output judge** checking that the answer is accurate, on-topic and safe.</span>

<span style="color:red">None of this is a security boundary. Nothing in a system prompt or CLAUDE.md is security; the only real control is **not giving the model tools** the human didn't authorize. Once the user is authenticated, hand over to a different agent instance whose MCP server enforces [row-level security](https://wiki.postgresql.org/wiki/Row-security) on the JWT `sub`, so the database itself refuses other users' rows, rather than asking the model to "please only read row 42" (see [[jwt-identity]], [[dual-leg-rule]], [[guardrails]]).</span>

## Judge as aggregator, not final authority

<span style="color:red">In detection products the LLM works better as an **aggregator and explainer** than as the final verdict. In a phishing-scanner exercise ([[2026-06-08-ai-security-tools]]): aggregate the existing scanners and reputation APIs (sites not yet flagged by reputation feeds can still be phishing); let the LLM correlate logo, image sources, text, page structure and known phishing-kit patterns; output a **confidence 0–1 with explicit evidence**; keep a human-analyst feedback loop ("wrong — why?"); and calibrate the final score on historical data and false positives. The same aggregate-and-explain shape fits [[agentic-review-pipeline]] verdicts.</span>

## See also
- [[guardrails]]
- [[prompt-injection]]
- [[system-prompt]]
- [[advisor]]
- <span style="color:red">[[jwt-identity]]</span>
- <span style="color:red">[[dual-leg-rule]]</span>
- <span style="color:red">[[example-driven-prompting]]</span>
- <span style="color:red">[[agentic-review-pipeline]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
