---
title: Judge LLM
category: security
tags: [security, guardrails, classifier, llm-as-judge, adversarial]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A judge LLM is a separate, cheap classifier model that screens both the incoming user prompt and the outgoing response for safety and topicality before the reply reaches the user.

## How it works

The judge runs independently of the main chat model. It receives the message under review and answers two binary questions: *Is this legitimate? Is it on-topic?* If either answer is no, it early-returns a refusal and logs the event into [[chat-history]] — the main model never runs (or its response is suppressed). This is the `JudgeGuard` pattern demonstrated in [[2026-06-10-spring-ai-itkonekt]].

**Why it works — two complementary reasons:**

1. **Clear mind.** The judge has no conversation context; it reviews with fresh eyes, making it harder to drift via multi-turn manipulation (see [[prompt-injection]]).
2. **Dice twice.** An attacker must beat both models independently. Even if the main model is fooled, the judge is a second, uncorrelated checkpoint.

## Model choice

Use a fast, cheap turbo/mini model as judge — never your main model. Cost and latency are the only real downsides of this pattern. For stronger coverage, run **different model families** as judges, and split concerns: one judge for safety, another for legal, another for topicality. Correlated judges from the same family share the same blind spots.

Real-world deployments confirm the pattern is mainstream: ChatGPT runs two judges; Anthropic's [[tool-calling]] infrastructure uses a strong model as judge on high-risk tool output (e.g., WebFetch over untrusted websites).

## See also
- [[guardrails]]
- [[prompt-injection]]
- [[system-prompt]]
- [[advisor]]
- [[2026-06-10-spring-ai-itkonekt]]
