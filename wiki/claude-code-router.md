---
title: Claude Code Router
category: tool
tags: [claude-code, provider-routing, openai, cost, agent-harness, sovereignty]
sources: ["[[2026-06-10-spring-ai-itkonekt]]", "[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-10
updated: 2026-06-23
---

A proxy layer that intercepts Claude Code (CloudCode) requests and forwards them to an alternative LLM provider — letting you run the full Claude Code harness on top of, say, OpenAI GPT, without changing your tooling or workflow.

## How it works

[Claude Code Router (CCR)](https://github.com/musistudio/claude-code-router) sits between the Claude Code CLI and the inference backend. From the user's perspective you read Opus; under the hood the requests are rewritten and sent to a different model and API key (e.g. GPT-4o via the OpenAI key). The harness — tool definitions, system prompts, conversation management — stays intact.

A fitting characterization: "you read Opus but it's actually GPT." It **works**, but there is a cost warning: routing through API keys rather than subscription pricing removes the flat-rate economics, and high-volume agentic sessions can become expensive quickly. The major harnesses (Claude Code, Codex) are converging rapidly as each borrows patterns from the other.

## When it is useful

CCR is useful for teams already standardized on OpenAI billing who want the Claude Code interaction model, or for side-by-side comparisons of harness quality across providers without re-learning a new CLI. It also matters for organizations where data-residency policy governs which inference endpoint can be called — the router gives you [[harness]] portability across compliant providers. [[token-economy]] considerations still apply: the tool schemas injected per turn are the same regardless of the backend model. Combine with the [[ralph-loop]] for overnight runs where provider cost per token is the controlling variable.

**The sovereignty story.** The most interesting target is an **open-weights** model you run **on-prem** — a DeepSeek coding model claimed near-Sonnet quality at ≈10% of the cost, runnable on a ≈half-million-dollar GPU box ("we don't depend on Trump"). CCR lets you point the same harness — same skills, same hooks — at it. Two caveats: take "it *is* Sonnet on every benchmark" as marketing (the gap is task-dependent), and note that routing to a *hosted* DeepSeek API means your code leaves your perimeter — the on-prem deployment is what actually buys sovereignty. CC-specific features (front-matter, hooks) may not fully port to a non-CC backend. <span style="color:red">This is the strongest answer to [[data-governance]]: when no [[data-governance|ZDR]] agreement exists with a cloud provider, routing the harness at an on-prem open model ([[ollama|self-hosted]]) keeps code and prompts inside the perimeter entirely.</span>

## See also

- [[token-economy]]
- [[ralph-loop]]
- [[model-context-protocol]]
- [[chat-model]]
- [[harness]]
- [[data-governance]]
- [[ollama]]
- [[2026-06-10-spring-ai-itkonekt]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
