---
title: Caveman (Terse-Output Skill)
category: pattern
tags: [anti-pattern, attention, token-economy, verbosity, human-factors, output-tokens]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-08-10-ai-consultancy]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

<span style="color:red">Caveman is a community skill that makes the agent answer telegraphically — "like a caveman" (*"Token expiry check. Fix!"*) — to cut **output** tokens; Victor treats it as an anti-pattern because it saves little money and taxes the one resource you can least afford: your own attention.</span>

## <span style="color:red">What it is</span>

<span style="color:red">[Caveman](https://github.com/JuliusBrussee/caveman) (created April 2026) rewrites only the agent's prose narration into terse fragments; code, diffs and commands are left unchanged. It advertises *"65% output token saved"*. (An earlier version of this page described it backwards, as a tool that *decompresses* terse output — the skill compresses.) Since then the project has grown into a suite with input compression and model routing.</span>

<span style="color:red">What it does **not** shrink: the thinking. Reasoning is billed as output regardless of the tone of the final answer, so if output cost is the worry, turn down [[effort-setting|effort]]; and it doesn't touch tool calls or tool results ([[2026-06-05-ai-consultancy]]).</span>

## <span style="color:red">Measured: 65% promised, 8.5% real</span>

<span style="color:red">JetBrains [measured it](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/) (July 2026) on 82 paired SkillsBench tasks with the skill forced on: **8.5% fewer output tokens**, no quality loss — *"Advertised saving: 65%. Measured saving: 8.5%."* And since forcing it on is a ceiling, real-world savings are lower. The repo's own README now admits that on top of a plain "be concise" instruction, `/caveman` cuts only **≈3% more at the median**.</span>

<span style="color:red">So the cheap alternative does most of the work. Sessions quoted different ratios for how much of Caveman's effect a one-line instruction (`Be brief` / `be concise` in [[agents-md|AGENTS.md]]) captures — ≈50% ([one blog benchmark](https://www.maxtaylor.me/articles/i-benchmarked-caveman-against-two-words)), ≈80%, ≈90%, ≈95% — the figures were not measured consistently, but the direction is the same and matches the README's 3% ([[2026-09-07-ai-bank]], [[2026-09-14-ai-industrial]], [[2026-09-18-ai-agentic-how]], [[2026-09-24-ai-bank]]).</span>

## Why it's harmful

The instinct behind it is friendly (save tokens), but it loads your **mental bandwidth** — the real last bottleneck of [[agentic-engineering]]. <span style="color:red">Telegraphese is a tax you pay with your own focus: *"you sacrifice your most precious resource, your brain bandwidth"*.</span> The fix is a plain instruction instead: tell the model *"keep explanations concise, I know what I'm doing."*

<span style="color:red">The line to draw is **concise-but-readable vs. compressed**. *"Be terse — why use many word when few do trick"* in CLAUDE.md is fine ([[2026-05-13-ai-it-services]]); a skill that makes you decode fragments is not. Tuning options that keep prose readable:</span>
- <span style="color:red">Name the audience: *"for experienced backend developers"* tunes tone and depth.</span>
- <span style="color:red">Claude Code [output styles](https://code.claude.com/docs/en/output-styles): `/config outputStyle=concise` when it dazzles you with stars and chapters; the `learning` style instead leaves you a method to write and checks it.</span>
- <span style="color:red">Give every CLI you build a **quiet flag**, so tool output stays small at the source ([[2026-09-18-ai-agentic-how]]).</span>

## Protect attention, not just tokens

Caveman is the mirror image of the [[token-economy]] tools: [[rtk]] and [[toon]] compress what the *model* reads to save money; <span style="color:red">Caveman compresses what the *human* reads and pays for it in focus.</span> (The joke that dropping a trailing period saves a token is exactly the wrong lens — one input token is noise; your attention is not.) Guarding attention is the same battle as resisting the [[ai-addiction]] dopamine loop and protecting the deep-thinking of [[plan-mode]]. A terse-output skill is "saving tokens on the wrong end" — it compresses the *AI's* output and makes *you* decode it: *"the weakest link is us; I want to protect my brain stamina."* The right place to compress is the CLI tool-result side (see [[rtk]] / [[headroom]]), which the human never has to read. <span style="color:red">Even there, measure the bill rather than trusting the tool's own counter: independent benchmarks found RTK roughly cost-neutral and Headroom a net loss (see [[rtk]]).</span>

## See also
- [[token-economy]]
- [[rtk]]
- [[ai-addiction]]
- [[plan-mode]]
- [[wispr-flow]]
- [[headroom]]
- <span style="color:red">[[effort-setting]]</span>
- <span style="color:red">[[agents-md]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
