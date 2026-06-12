---
title: TOON (Token-Oriented Object Notation)
category: tool
tags: [token-economy, data-format, json, csv, cost-optimization]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


TOON (Token-Oriented Object Notation) is a "CSV-for-AI" data format that encodes structured data far more compactly than JSON by not repeating field names — saving input tokens on large collections.

## Where it shines

[TOON](https://github.com/toon-format/toon) targets the redundancy in JSON: a sample payload went **86 → 38 tokens**. The win is largest on **collections of records**, because the field names are declared once for the whole table instead of on every object — exactly where JSON is most wasteful. It is the payload-shaping complement to [[rtk]] (which trims command *output*), both serving the same [[token-economy]] goal.

## The benchmark caveat

A sharp objection from the room: *how do you know the model parses TOON as well as JSON, and doesn't burn more reasoning tokens decoding it?* A format the model saw rarely in training can cost **more** output/reasoning tokens than it saves on input — so you must measure **end-to-end token cost**, not just payload size. The honest answer is to benchmark: run the same task 50× and look at the mean and standard deviation. This is the job for a team's "AI researcher" — the one person in a white coat who measures rather than guesses.

## See also
- [[token-economy]]
- [[rtk]]
- [[context-window]]
- [[2026-06-11-ai-playtika]]
