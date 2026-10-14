---
title: Ollama
category: tool
tags: [ollama, local-llm, self-hosted, gdpr, open-weights, zdr]
sources: ["[[2026-06-10-spring-ai-open-workshop]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-03-ai-bank]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]"]
created: 2026-06-10
updated: 2026-10-13
---

[Ollama](https://ollama.com/) is a runtime for executing open-weight LLMs entirely on local hardware, eliminating outbound data transfer to third-party APIs.

The workshop used Ollama running **Qwen 2.5**, activated in [[spring-ai]] by switching to the `local` Spring profile, which swaps `OpenAiChatModel` for `OllamaChatModel` — the [[chat-model]] interface is identical, so no application code changes. This provider-agnostic swappability is one of Spring AI's headline selling points.

**Why run locally?** The primary driver is data residency. Companies processing customer personal data are often legally or contractually blocked from sending that data to US-owned cloud APIs. The relevant legal framework is GDPR / data-transfer rules (not the EU AI Act, which regulates AI by risk tier rather than mandating on-premises processing — see [[2026-06-10-spring-ai-open-workshop]] for the correction). The result in practice: in-house LLMs for GDPR-sensitive workloads are common, especially in European enterprises.

<span style="color:red">**Weights vs temperature, open-weight vs open-source.** The weights are the hundreds of GB of floats that *are* the model; temperature is just a sampling parameter applied at inference time. **Open-weight ≠ open-source:** Llama, DeepSeek and Mistral publish weights but not their training data — closer to freeware than to OSI open source ([[2026-05-13-ai-it-services]]).</span>

**Trade-offs:** Local models are noticeably slower and weaker than frontier models like GPT-4o-mini. Qwen 2.5 cracks jailbreaks that GPT-4o-mini resists, so [[guardrails]] need calibrating per model. A capable GPU is required; Apple Silicon Macs have an advantage because system RAM and GPU memory are unified, letting a 32 GB Mac run models that would need a dedicated GPU card on x86.

For any agent workflow where confidential data must stay on-premises, pairing Ollama with [[spring-ai]]'s advisor pipeline and a local [[judge-llm]] is the canonical local stack.

<span style="color:red">**GPU economics.** Qwen runs a ≈100k context on a single 24 GB GPU at ≈10% of the cost — yet cheap cloud models still beat local ones on quality per euro. The trainer's advice for an individual: "a ≈€90/month Claude subscription for two months makes you a different developer"; the organisation will inevitably lag behind ([[2026-07-03-ai-bank]]).</span>

**Tiered model architecture: Opus orchestrator + local Qwen.** The emerging production pattern: use Opus or Sonnet only for the orchestration layer and judgement-heavy tasks, and delegate cheap, scoped subtasks (log scanning, support-bot responses, deterministic extraction) to a Qwen model running on a company server via Ollama. This avoids burning Sonnet on trivial work while keeping sensitive data on-premises. Constraint: local tiers are not suitable for production code authorship or long-horizon reasoning.

**Open vs frontier, and what local actually buys you.** An LLM is *"a few hundred gigabytes of floating-point weights."* **Open** models like [DeepSeek](https://huggingface.co/deepseek-ai) you can actually download (its weights live on a Hugging Face page) — DeepSeek is cited at ≈Sonnet quality for ≈10% of the price — but self-hosting needs serious hardware (≈900 GB RAM, ≈$500K of kit). **Frontier proprietary** models (GPT, Opus) can *never* be downloaded — they always run in a vendor datacenter. Running open weights on-prem is the strongest form of [[data-governance]]: when there's no [[data-governance|ZDR]] agreement with a cloud provider, local inference keeps the data inside the perimeter entirely. Point the harness at it with [[claude-code-router]].

## <span style="color:red">The open-weight field in autumn 2026</span>

<span style="color:red">Open weights broke the frontier labs' monopoly. Sessions from August 2026 named **DeepSeek V4 Pro**, **GLM-5.2** and **Kimi** as the self-hostable contenders, with no political/vendor risk once the weights are on your own disk ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]]). One session's rough equivalence table, built from Artificial Analysis comparisons:</span>

| <span style="color:red">Open model</span> | <span style="color:red">≈ Frontier peer</span> | <span style="color:red">≈ Cheaper by</span> |
|---|---|---|
| <span style="color:red">DeepSeek V4 Pro</span> | <span style="color:red">[Sonnet 5](https://artificialanalysis.ai/models/comparisons/claude-sonnet-5-vs-deepseek-v4-pro)</span> | <span style="color:red">≈10×</span> |
| <span style="color:red">GLM-5.2</span> | <span style="color:red">[Opus 5 (xhigh)](https://artificialanalysis.ai/models/comparisons/claude-opus-5-xhigh-vs-glm-5-2)</span> | <span style="color:red">≈5×</span> |
| <span style="color:red">Kimi K3</span> | <span style="color:red">[Fable 5](https://artificialanalysis.ai/models/comparisons/kimi-k3-vs-claude-fable-5)</span> | <span style="color:red">≈2×</span> |

<span style="color:red">Treat the multipliers as a snapshot, not a constant ([[2026-08-13-ai-insurer]], [[2026-08-10-ai-consultancy]]). Without half a million dollars of hardware, rent GPUs by the hour on [Vast.ai](https://vast.ai/). And don't calibrate your practices on today's price: the sessions' rule of thumb is that inference gets ≈10× cheaper per year (a trainer heuristic, not a measured figure). For current Anthropic list prices see [[token-economy]].</span>

## See also
- [[spring-ai]]
- [[chat-model]]
- [[guardrails]]
- [[judge-llm]]
- [[context-window]]
- [[dual-leg-rule]]
- [[data-governance]]
- [[claude-code-router]]
- [[model-hierarchy]]
- <span style="color:red">[[token-economy]]</span>
- [[2026-06-10-spring-ai-open-workshop]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
