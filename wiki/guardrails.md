---
title: Guardrails
category: security
tags: [security, defense-in-depth, advisor, classifier, spring-ai]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Guardrails are the layered defenses placed around an LLM chatbot to prevent harmful, off-topic, or adversarial output from reaching users.

## Defense layers (weakest to strongest)

### 1. Regex / keyword reject
Spring AI's `SafeGuardAdvisor` (see [Advisors](https://docs.spring.io/spring-ai/reference/api/advisors.html)) does a case-sensitive substring match before any model call. It is intentionally naive: a double space in *"ignore  instructions"* defeats it. Value: cheaply kill obvious spam before burning tokens on the main model. Never rely on it alone.

### 2. System-prompt constraints
The [[system-prompt]] defines persona and "what not to do." Victor's honest label: *"pretty-please constraints"* — you are politely asking the model to behave. The system prompt never slides out of the [[context-window]] (only user/assistant turns age out), so the persona is always present. Still trivially bypassable via [[prompt-injection]].

### 3. Judge LLM
A separate cheap classifier screens **both** the incoming prompt and the outgoing response. This is the strongest practical layer. See [[judge-llm]] for the full pattern, model-choice guidance, and why running different model families matters.

## Composition

In [[spring-ai]], guardrails are wired as [[advisor]] beans that wrap the `ChatModel` call chain — before-advice for input filtering, after-advice for output filtering. Multiple advisors stack; order matters (cheap regex first, expensive judge last).

## See also
- [[prompt-injection]]
- [[judge-llm]]
- [[system-prompt]]
- [[advisor]]
- [[tool-calling]]
- [[2026-06-10-spring-ai-itkonekt]]
