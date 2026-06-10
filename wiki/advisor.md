---
title: Advisor
category: concept
tags: [spring-ai, advisor, middleware, aop, memory]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

A Spring AI Advisor is middleware that intercepts an LLM call — running logic **before** (to augment the request) and **after** (to process the response) — analogous to a Spring AOP aspect or `@Cacheable`.

## Mental model

> "An advisor is like a Spring aspect / `@Cacheable`: it runs *before* the call (inject memory) and *after* (save response, evict the oldest)."
> — [[2026-06-10-spring-ai-itkonekt]]

Advisors are chained. Spring AI evaluates them in order around the actual `ChatModel.call()` invocation.

## Built-in advisors

| Advisor | What it does |
|---|---|
| `MessageChatMemoryAdvisor` | Prepends the last N messages ([[chat-memory]] sliding window) before the call; appends the response after. |
| `SafeGuardAdvisor` | Keyword/regex match on the prompt — cheaply rejects obvious spam before burning tokens. Naive: a single inserted space defeats it. |
| `QuestionAnswerAdvisor` | [[rag]] — runs a vector similarity search and injects retrieved chunks into the prompt. |
| `JudgeGuard` (custom) | A separate cheap LLM screens both the incoming prompt and the outgoing response; early-returns a refusal if either looks off. See [[judge-llm]]. |

Official docs: [Spring AI Advisors](https://docs.spring.io/spring-ai/reference/api/advisors.html)

## Ordering matters

A `SafeGuardAdvisor` placed first kills cheap obvious spam before the memory advisor fires, saving token cost. A `JudgeGuard` placed last screens the final response before it reaches the user.

## See also

- [[chat-memory]]
- [[chat-history]]
- [[guardrails]]
- [[judge-llm]]
- [[rag]]
- [[context-window]]
