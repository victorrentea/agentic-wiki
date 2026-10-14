---
title: Uncensored / Abliterated Models
category: security
tags: [security, open-weights, safety-training, red-teaming, abliteration, jailbreak]
sources: ["[[2026-09-28-ai-gaming-tech]]", "[[2026-07-20-ai-telecom]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-08-31-ai-software-vendor]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Uncensored (or "abliterated") models are open-weight models whose refusal behaviour has been stripped out. They are dangerous in anyone's hands, and also the tool you need to red-team your own agent sandbox, because aligned frontier models refuse to attack it.

## How refusal is added and removed

- **Safety training is fine-tuning on top of the base model.** RLHF-style training teaches the model to refuse. One session caricatured it as "repeating a million times 'don't say how to make a bomb'" ([[2026-07-20-ai-telecom]]). The knowledge stays in the weights; only the willingness to say it changes.
- **Abliteration reverses it.** Refusal can be located in the model's activations and edited out of open weights without retraining from scratch ([arXiv 2406.11717](https://arxiv.org/abs/2406.11717)). Tools such as [heretic](https://github.com/p-e-w/heretic) automate this for any open-weight model, and uncensored builds of popular open families (for example Qwen) circulate on Hugging Face ([[2026-09-28-ai-gaming-tech]], [[2026-09-07-ai-bank]]).
- **Only open weights can be abliterated.** Frontier proprietary models never leave the vendor's datacenter, so their guardrails can only be attacked through prompts (see [[jailbreak-ladder]]), never removed.

This is why the wiki calls a [[system-prompt]] a "pretty-please" guardrail. An uncensored model running behind one breaks far more easily than a frontier model.

## Why a defender wants one

To test whether your [[docker-sandboxing|sandbox]], [[os-sandbox]] or [[agent-permissions]] actually hold, you need an agent that *tries* to escape: read `~/.ssh`, exfiltrate, call the network. Aligned frontier models refuse those instructions, so they make poor attackers. An abliterated local model on [[ollama]] plays the hostile agent you are defending against ([[2026-09-28-ai-gaming-tech]]). Run it **only inside the sandbox under test**, never on a machine that holds real credentials.

## Read the headlines carefully

Most "AI goes rogue" stories describe *pre-safety* or research models cheating on evaluations, not the aligned models you use in production ([[2026-09-28-ai-gaming-tech]]). The real risks are more mundane: [[reward-hacking]] in your own loops, and [[prompt-injection]] steering a perfectly aligned model.

## Open weights ≠ open source

"Open" models publish weights, not training data. You cannot audit what was trained in: biases, or in the worst case a behaviour planted during training that no inspection of the weights will reveal ([[2026-08-31-ai-software-vendor]], [[2026-09-16-ai-safety-critical]]). That makes a downloaded model one more local-execution vector next to MCP servers, skills and plugins (see [[supply-chain-attack]]). It is also a [[data-governance]] question: running on-prem keeps your data in, but it does not make the model trustworthy.

## See also
- [[jailbreak-ladder]]
- [[docker-sandboxing]]
- [[os-sandbox]]
- [[ollama]]
- [[system-prompt]]
- [[data-governance]]
- [[supply-chain-attack]]
- [[reward-hacking]]
- [[2026-09-28-ai-gaming-tech]]
