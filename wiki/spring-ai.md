---
title: Spring AI
category: tool
tags: [spring-ai, java, llm-framework, chat-model, advisor]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

Spring AI is a provider-agnostic Java framework for building LLM-powered applications, offering a uniform [[chat-model]] abstraction, an [[advisor]] pipeline, [[tool-calling]], and [[rag]] support. See the [official docs](https://docs.spring.io/spring-ai/reference/index.html).

The central abstraction is `ChatModel`. You swap between `OpenAiChatModel` (GPT-4o-mini) and `OllamaChatModel` ([[ollama]] + Qwen 2.5) purely by activating the `local` Spring profile — zero code change. Gemini, Anthropic, and others each have a dedicated starter.

Memory and context management are handled through the **Advisors API** — interceptors that run before and after each LLM call. The `MessageChatMemoryAdvisor` prepends the last N turns of [[chat-memory]] to every request and appends the response afterward; think of it as `@Cacheable` for conversations, or a Spring AOP aspect around the model call. The [[system-prompt]] is injected once and never slides out of the [[context-window]], while user/assistant turns rotate through the sliding window.

The Pet Clinic workshop chatbot (built live at [[2026-06-10-spring-ai-itkonekt]]) demonstrates the full stack: a Spring Boot process on port 8082 hosts the chat UI, wires advisors, connects to the LLM, calls local tools (time, email), and reaches a backend over [[model-context-protocol]]. [[rag]] is layered on via `QuestionAnswerAdvisor` backed by [[pgvector]] (or a file store when Docker is off-limits). The `SafeGuardAdvisor` provides a cheap regex pre-filter before a [[judge-llm]] handles deeper [[guardrails]].

For multi-step *agentic* workflows (not just single chat turns), Rod Johnson — the original creator of Spring — now builds [[embabel]], a separate JVM framework that complements this chat layer.

## See also
- [[chat-model]]
- [[advisor]]
- [[chat-memory]]
- [[chat-history]]
- [[tool-calling]]
- [[rag]]
- [[ollama]]
- [[model-context-protocol]]
- [[judge-llm]]
- [[guardrails]]
- [[pgvector]]
- [[embabel]]
- [[2026-06-10-spring-ai-itkonekt]]
