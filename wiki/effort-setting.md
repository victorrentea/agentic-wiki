---
title: Effort Setting
category: concept
tags: [reasoning, configuration, token-economy, model-choice, claude-code]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


The effort setting is the knob that controls how much reasoning a model spends per turn — and the practical advice is to use **xhigh** for everything and **never set it to max**.

## Why not max

At **max**, the model "starts talking about the meaning of life / why we write tests" and burns enormous reasoning tokens for little gain — a [[token-economy]] sink with diminishing returns. **xhigh** gives near-equivalent quality without the philosophizing, so it is the sensible default for real work.

## Effort vs model choice

Effort is one of two reasoning dials, the other being which model you run:

- **Downgrade Opus → Sonnet** for simple work to save cost and context.
- **Don't use Haiku at the office** — reserve it for fast scans or a single-file rename, never for engineering.

Both dials feed the same [[token-economy]] discipline and interact with the [[dumb-zone]]: more reasoning fills the [[context-window]] faster, so spend it where it earns its keep.

## See also
- [[token-economy]]
- [[dumb-zone]]
- [[context-window]]
- [[2026-06-11-ai-playtika]]
