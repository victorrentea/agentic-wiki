---
title: Caveman (Anti-Pattern)
category: pattern
tags: [anti-pattern, attention, token-economy, verbosity, human-factors]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-23-ai-garmin]]"]
created: 2026-06-11
updated: 2026-06-23
---


Caveman is the anti-pattern of a tool that *decompresses* a model's terse output into long explanations — taxing the one resource you can least afford: your own attention.

## Why it's harmful

The instinct behind it is friendly (make the AI explain more), but it loads your **mental bandwidth** — the real last bottleneck of [[agentic-engineering]]. Verbose AI prose is a tax you pay with your own focus, not with tokens. The fix is the opposite instruction: tell the model *"keep explanations concise, I know what I'm doing."*

## Protect attention, not just tokens

Caveman is the mirror image of the [[token-economy]] tools: [[rtk]] and [[toon]] compress what the *model* reads to save money; Caveman *expands* what the *human* reads and costs focus. (The joke that dropping a trailing period saves a token is exactly the wrong lens — one input token is noise; your attention is not.) Guarding attention is the same battle as resisting the [[ai-addiction]] dopamine loop and protecting the deep-thinking of [[plan-mode]]. <span style="color:red">A terse-output skill is "saving tokens on the wrong end" — it compresses the *AI's* output and makes *you* decode it: *"the weakest link is us; I want to protect my brain stamina."* The right place to compress is the CLI tool-result side (see [[rtk]] / [[headroom]]), which the human never has to read.</span>

## See also
- [[token-economy]]
- [[rtk]]
- [[ai-addiction]]
- [[plan-mode]]
- [[wispr-flow]]
- [[headroom]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-23-ai-garmin]]
