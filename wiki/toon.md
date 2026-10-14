---
title: TOON (Token-Oriented Object Notation)
category: tool
tags: [token-economy, data-format, json, csv, cost-optimization]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]"]
created: 2026-06-11
updated: 2026-10-13
---

TOON (Token-Oriented Object Notation) is a "CSV-for-AI" data format that encodes structured data far more compactly than JSON by not repeating field names — saving input tokens on large collections.

## Where it shines

[TOON](https://github.com/toon-format/toon) targets the redundancy in JSON: a sample payload went **86 → 38 tokens**. The win is largest on **collections of records**, because the field names are declared once for the whole table instead of on every object — exactly where JSON is most wasteful. It is the payload-shaping complement to [[rtk]] (which trims command *output*), both serving the same [[token-economy]] goal.

<span style="color:red">**Where to apply it:** wrap chatty [[model-context-protocol|MCP]] servers that return JSON with a post-tool hook ([[agent-lifecycle-hooks]]) that converts JSON → TOON before the result reaches the context ([[2026-05-27-ai-open-workshop]]).</span>

## The benchmark caveat

A sharp objection from the room: *how do you know the model parses TOON as well as JSON, and doesn't burn more reasoning tokens decoding it?* A format the model saw rarely in training can cost **more** output/reasoning tokens than it saves on input — so you must measure **end-to-end token cost**, not just payload size. The honest answer is to benchmark: run the same task 50× and look at the mean and standard deviation. This is the job for a team's "AI researcher" — the one person in a white coat who measures rather than guesses.

<span style="color:red">The claimed savings already disagree between sessions — ≈50% in one, ≈20% in another, with the 86 → 38 sample above in between ([[2026-05-27-ai-open-workshop]], [[2026-06-02-ai-open-workshop]]) — which is exactly why the end-to-end benchmark matters.</span> <span style="color:red">The cautionary precedent is [[rtk]]: JetBrains' paired benchmark found RTK's output trimming **+7.6% more expensive** at low effort and cost-neutral at high effort, because the agent took more turns ([JetBrains, Jul 2026](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/)) — fewer tokens in the payload is not a smaller bill. See [[agent-evals]].</span>

## See also
- [[token-economy]]
- [[rtk]]
- [[context-window]]
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[agent-evals]]</span>
- <span style="color:red">[[headroom]]</span>
- [[2026-06-11-ai-mobile-gaming]]
