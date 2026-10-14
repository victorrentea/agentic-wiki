---
title: Chatbot Latency Tuning
category: pattern
tags: [performance, latency, spring-ai, observability, tracing, chatbot]
sources: ["[[2026-08-03-ai-telecom-ops-qa]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Fixing a slow in-house LLM chatbot (e.g. 60 s per answer) from measured traces rather than gut feeling.

## Measure first: trace across the LLM boundary

Propagate the W3C `traceparent` header into every MCP tool and into the LLM server, so a single trace shows where the 60 s actually go — model thinking, sequential tool calls, or a slow backend behind a tool. If the LLM provider or gateway doesn't propagate it, escalate: without it you are tuning blind ([[telemetry-by-default]], [[observability-mcp]]).

## The levers

1. **Programmatic pre-loading.** Find what ≈90% of requests need and fetch it *before* the model asks. Example: a regex on the user message detects a SIM or account number and pre-fetches its data into the prompt, instead of waiting a full round-trip for the LLM to request it via a tool. This is [[determinism-over-llm]] applied to latency.
2. **Explicit parallel tool calls.** Instruct parallelisation in the [[system-prompt]]; otherwise the model tends to call independent tools one after another, each adding a full model turn ([[tool-calling]]).
3. **Streaming** (`Flux<String>` instead of `String` in [[spring-ai]]). It wins ≈10 s of *perceived* latency — the first words appear sooner — but no real time.

## Don't forget the waste

Off-topic and jailbreak traffic burns budget and capacity too; a cheap screen in front of the main model ([[jailbreak-ladder]], [[judge-llm]]) cuts load that would otherwise queue ahead of real users.

## See also
- [[telemetry-by-default]]
- [[spring-ai]]
- [[tool-calling]]
- [[sub-agents]]
- [[jailbreak-ladder]]
- [[determinism-over-llm]]
- [[2026-08-03-ai-telecom-ops-qa]]
