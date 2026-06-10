---
title: Ollama
category: tool
tags: [ollama, local-llm, self-hosted, gdpr, open-weights]
sources: ["[[2026-06-10-spring-ai-itkonekt]]"]
created: 2026-06-10
updated: 2026-06-10
---

[Ollama](https://ollama.com/) is a runtime for executing open-weight LLMs entirely on local hardware, eliminating outbound data transfer to third-party APIs.

The workshop used Ollama running **Qwen 2.5**, activated in [[spring-ai]] by switching to the `local` Spring profile, which swaps `OpenAiChatModel` for `OllamaChatModel` — the [[chat-model]] interface is identical, so no application code changes. This provider-agnostic swappability is one of Spring AI's headline selling points.

**Why run locally?** The primary driver is data residency. Companies processing customer personal data are often legally or contractually blocked from sending that data to US-owned cloud APIs. The relevant legal framework is GDPR / data-transfer rules (not the EU AI Act, which regulates AI by risk tier rather than mandating on-premises processing — see [[2026-06-10-spring-ai-itkonekt]] for the correction). The result in practice: in-house LLMs for GDPR-sensitive workloads are common, especially in European enterprises.

**Trade-offs:** Local models are noticeably slower and weaker than frontier models like GPT-4o-mini. Qwen 2.5 cracks jailbreaks that GPT-4o-mini resists, so [[guardrails]] need calibrating per model. A capable GPU is required; Apple Silicon Macs have an advantage because system RAM and GPU memory are unified, letting a 32 GB Mac run models that would need a dedicated GPU card on x86.

For any agent workflow where confidential data must stay on-premises, pairing Ollama with [[spring-ai]]'s advisor pipeline and a local [[judge-llm]] is the canonical local stack.

## See also
- [[spring-ai]]
- [[chat-model]]
- [[guardrails]]
- [[judge-llm]]
- [[context-window]]
- [[dual-leg-rule]]
- [[2026-06-10-spring-ai-itkonekt]]
