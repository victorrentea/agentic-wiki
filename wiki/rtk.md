---
title: RTK (Rust Token Killer)
category: tool
tags: [token-economy, cli, output-tokens, hook, cost-optimization]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
---


RTK (Rust Token Killer) is a bash-command proxy that runs a shell command, polishes its output, and hands the model only what matters — cutting the output tokens that dominate agent cost.

## How it works

[RTK](https://github.com/rtk-ai/rtk) auto-configures via a hook so that every `git status` quietly becomes `rtk git status`, transparently and at zero prompt overhead. It intercepts the command, executes it, and strips the noise: a raw `git status` of 97 tokens drops to **19**. This attacks the expensive side of the budget — **output is ≈5× more expensive than input** — and the same idea generalizes: when you are repeatedly consuming a tool's fixed, structured output, script the compaction once rather than paying a model to re-read it (see [[agent-skill]] and the JaCoCo-to-script lesson).

## The honest number

RTK's advertised "80% savings" is marketing; in practice **expect ≈30%**. That is still real money on a high-volume agentic workflow, and it stacks with the rest of [[token-economy]] discipline. It is the same philosophical move as [[toon]] (compress the payload) — but where TOON reshapes data, RTK trims command output.

## See also
- [[token-economy]]
- [[toon]]
- [[agent-skill]]
- [[context-window]]
- [[2026-06-11-ai-playtika]]
