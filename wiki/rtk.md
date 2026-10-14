---
title: RTK (Rust Token Killer)
category: tool
tags: [token-economy, cli, output-tokens, hook, cost-optimization]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-06-11
updated: 2026-10-13
---

RTK (Rust Token Killer) is a bash-command proxy that runs a shell command, polishes its output, and hands the model only what matters — <span style="color:red">trimming the command output that would otherwise sit in the agent's context and be re-read on every turn.</span>

## How it works

[RTK](https://github.com/rtk-ai/rtk) auto-configures via a hook so that every `git status` quietly becomes `rtk git status`, transparently and at zero prompt overhead. <span style="color:red">In Claude Code that hook is a `PreToolUse` rewrite ([[agent-lifecycle-hooks]]).</span> It intercepts the command, executes it, and strips the noise: a raw `git status` of 97 tokens drops to **19** <span style="color:red">(another session measured 102 → 24)</span>. <span style="color:red">It doesn't truncate: it rewrites output into a compact form — a verbose `ls` becomes a compact tree, `git push` chatter becomes "ok, on main" — using per-tool rule sets (git, ruff, cargo, pytest, go…). The vendor says this happens "without semantic loss"; that is its claim, not a measurement ([[2026-06-05-ai-consultancy]]).</span>

<span style="color:red">**Escape hatch:** `rtk proxy <cmd>` runs the raw, unfiltered command — use it to check whether compaction ate a detail before blaming the model.</span>

<span style="color:red">Earlier versions of this page said RTK "attacks the expensive side of the budget — output is ≈5× more expensive than input". That is a mix-up: command output enters the model's context as **input** tokens, and on later turns mostly as cheap cache reads ([[prompt-caching]]). The real lever is context size, not the output price.</span> The general idea still holds: when you are repeatedly consuming a tool's fixed, structured output, script the compaction once rather than paying a model to re-read it (see [[agent-skill]] and the JaCoCo-to-script lesson).

## The honest number: measured, not advertised

RTK's advertised savings are marketing — <span style="color:red">the repo claims "60–90%" fewer tokens on common dev commands ([[2026-06-02-ai-open-workshop]]); one session repeated "≈80%" ([[2026-06-08-ai-security-tools]]).</span> <span style="color:red">The measurements tell a different story:</span>

| <span style="color:red">Who</span> | <span style="color:red">Setup</span> | <span style="color:red">Result</span> |
|---|---|---|
| <span style="color:red">[JetBrains](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/), Jul 2026</span> | <span style="color:red">Claude Code + Sonnet 5, SkillsBench, 86 paired tasks, ≈425 billed trials</span> | <span style="color:red">**+7.6% more expensive** at low effort (p = 0.004), ±0% at high effort, quality unchanged — the extra cost came from more turns and cache reads</span> |
| <span style="color:red">[PointFive](https://arxiv.org/html/2607.12161v5), Jul 2026 (vendor-authored)</span> | <span style="color:red">RTK v0.44.1</span> | <span style="color:red">−2.7%, roughly break-even</span> |
| <span style="color:red">[Quesma](https://quesma.com/blog/does-rtk-make-ai-coding-cheaper/), Sep 2026</span> | <span style="color:red">Terminal-Bench 2.1</span> | <span style="color:red">per-task cost +1% (Fable) to +17% (DeepSeek)</span> |
| <span style="color:red">Training sessions</span> | <span style="color:red">own repos, informal</span> | <span style="color:red">≈10–15% real token savings ([[2026-08-03-ai-telecom-ops-qa]], [[2026-09-07-ai-bank]]); *"don't expect miracles"* ([[2026-08-06-ai-bank]])</span> |

<span style="color:red">The trap is RTK's own scoreboard: *"rtk's scoreboard said 96 million tokens saved while the invoice went up"* (JetBrains). `rtk gain` counts **bytes removed**, against a baseline that never existed — not money saved (Quesma). Token savings ≠ cost savings ([[2026-08-10-ai-consultancy]]). The earlier advice on this page, "expect ≈30%", was already sceptical; by autumn 2026 the honest expectation is **somewhere between a small saving and a small loss — measure on your own repo, with your own bill, before imposing it company-wide** ([[2026-08-13-ai-insurer]]).</span>

It is the same philosophical move as [[toon]] (compress the payload) — but where TOON reshapes data, RTK trims command output. <span style="color:red">TOON's case is stronger: DB result sets as header + rows instead of JSON carry the same data in ≈half the tokens ([[2026-08-13-ai-insurer]]).</span>

## Prefer native terse flags

<span style="color:red">The tools already have minimal-output modes the model knows: `git status -s` prints just `M file`, plus `--quiet`, `--porcelain`, `-q`. Prefer those, and make every CLI you hand to an agent offer a **minimal-output mode**; keep a proxy like RTK for tools with no quiet mode ([[2026-09-24-ai-bank]], [[2026-09-28-ai-gaming-tech]], [[2026-09-07-ai-bank]]). See [[token-economy]].</span>

## See also
- [[headroom]]
- [[token-economy]]
- [[toon]]
- [[agent-skill]]
- [[context-window]]
- <span style="color:red">[[caveman]]</span>
- <span style="color:red">[[prompt-caching]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
