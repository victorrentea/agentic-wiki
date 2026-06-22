---
title: Guardrails
category: security
tags: [security, defense-in-depth, advisor, classifier, spring-ai, jailbreak]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]"]
created: 2026-06-10
updated: 2026-06-12
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

The same layered-defence idea applies to an agent's *output code*, not just a chatbot's output text: the [[static-analysis-gauntlet]] (SonarQube / CodeQL / Semgrep as build-failing CI gates) and softer [[tripwire|tripwires]] are guardrails around what an agent writes, forcing self-repair before a human reviews.

**Each layer has a documented bypass.** The [[jailbreak-ladder]] maps the full escalation path (RLHF → abliteration → regex → judge LLM → context accumulation) and the specific technique that defeats each rung. None are guarantees; they're defense-in-depth. The only structural defense is removing a capability (see [[lethal-trifecta]]).

## See also
- [[prompt-injection]]
- [[judge-llm]]
- [[system-prompt]]
- [[advisor]]
- [[tool-calling]]
- [[static-analysis-gauntlet]]
- [[tripwire]]
- [[jailbreak-ladder]]
- [[lethal-trifecta]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
