---
title: Chat Model
category: concept
tags: [spring-ai, llm, abstraction, provider-agnostic, ollama]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

`ChatModel` is [[spring-ai]]'s provider-agnostic interface for sending messages to an LLM and receiving a response, with zero code change needed to swap providers.

## How it works

Spring AI ships a separate starter for each provider — OpenAI, Anthropic, Gemini, [[ollama]], and more. At runtime, Spring wires whichever implementation is on the classpath. The demo project uses `OpenAiChatModel` (GPT-4o-mini) by default and switches to `OllamaChatModel` running Qwen 2.5 locally by activating the `local` Spring profile. Nothing in application code changes; only `application-local.properties` differs.

```
# application-local.properties
spring.ai.ollama.chat.model=qwen2.5
```

## Why run local

Data-residency requirements drive many enterprises to self-host. Processing customer personal data with a third-party cloud LLM raises GDPR / data-transfer concerns even when no specific law flatly forbids it. [[ollama]] makes running a capable open model on a laptop practical.

## Practical notes

- Pin the model version. `gpt-4o-mini` silently rolls forward and can change [[guardrails]] behavior overnight.
- Temperature controls determinism — lower values produce more consistent refusals.
- The [[system-prompt]] and [[chat-memory]] are concerns *above* `ChatModel`; they live in the [[advisor]] layer.

Source: [[2026-06-10-spring-ai-itkonekt]]

External: [Spring AI reference](https://docs.spring.io/spring-ai/reference/index.html) · [Ollama](https://ollama.com/)

## See also

- [[spring-ai]]
- [[ollama]]
- [[advisor]]
- [[system-prompt]]
- [[chat-memory]]
- [[context-window]]
