---
title: Dumb Zone
category: concept
tags: [context-window, lost-in-the-middle, performance, compaction, recall, context-rot]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

The dumb zone (also called "context rot") is the region of a [[context-window]] — <span style="color:red">roughly past **≈55–65% full** in early-2026 sessions, quoted as **≈60–85%** by later ones —</span> where a model becomes "lost in the middle" and its reasoning quality visibly degrades, even on frontier models.

## Where it kicks in

The threshold is a fraction of the window, not an absolute token count, so it scales with the model. <span style="color:red">Remember the **percentage**, not a number: 65% of a 200k window is 130k, but Opus 5 and Sonnet 5 run on **1M**, where the absolute figure is entirely different ([[2026-08-27-ai-it-services]]).</span>

<span style="color:red">The operating thresholds quoted across the 2026 sessions, newest first (all rules of thumb, none published):</span>

| <span style="color:red">Window</span> | <span style="color:red">Stop / kill around</span> | <span style="color:red">Notes</span> |
|---|---|---|
| <span style="color:red">200k (Sonnet-class)</span> | <span style="color:red">**≈150k** (≈130–160k across sessions; ≈⅔ of the window)</span> | <span style="color:red">"Don't trust Sonnet past ≈160k" ([[2026-09-07-ai-bank]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]])</span> |
| <span style="color:red">1M (Opus-class)</span> | <span style="color:red">**≈600k** as a kill threshold; **≈500k** for ops work</span> | <span style="color:red">Earlier sessions said "impeccable under 300k" and quoted ≈300–400k; by autumn the ⅔-of-window reading (≈600–700k) took over ([[2026-08-06-ai-bank]], [[2026-08-10-ai-consultancy]], [[2026-09-16-ai-safety-critical]], [[2026-09-28-ai-gaming-tech]])</span> |
| <span style="color:red">any, heavy work (CLIs, browsers)</span> | <span style="color:red">**under ½, ⅔ at most**</span> | <span style="color:red">The conservative band ([[2026-09-24-ai-bank]])</span> |
| <span style="color:red">"smart zone"</span> | <span style="color:red">first **≈100k** tokens</span> | <span style="color:red">Matt Pocock's term and comfort zone, ≈100–150k ([AI Hero](https://www.aihero.dev/)) ([[2026-09-02-ai-insurer]], [[2026-09-16-ai-safety-critical]])</span> |

<span style="color:red">The most conservative operator's rule: **past ≈100k tokens in one conversation, expect slop** ([[2026-07-01-ai-java-community]]). Sonnet with a 1M window isn't worth it: a task that needs 1M is an Opus task ([[2026-06-02-ai-open-workshop]], [[2026-09-07-ai-bank]]).</span>

<span style="color:red">**Scale anchors** (sessions' estimates): a 50k-line Java microservice ≈600k tokens ([[2026-09-30-ai-enterprise]]); all of *War and Peace* ≈700–900k tokens; a human holds ≈60k tokens in working memory ([[2026-08-31-ai-software-vendor]], [[2026-09-07-ai-bank]]). Legacy code can burn **200–270k tokens** of reading before the tests even run, so delegate the reading to [[sub-agents]] ([[2026-10-13-ai-bank]]).</span>

A **1M-token window does not mean 1M tokens of usable reasoning**. <span style="color:red">Single-fact needle-in-a-haystack lookup is not the problem: it stays near-perfect at long lengths (Claude 3 Opus averaged >99% at 200k — [Anthropic](https://www.anthropic.com/news/claude-3-family); Google's [Gemini 1.5 report](https://arxiv.org/abs/2403.05530) shows similar single-needle recall at 1M). What degrades is **multi-fact retrieval and reasoning** over a full window. On the 8-needle MRCR v2 test at 1M, Opus 4.6 scored 76% vs Sonnet 4.5's 18.5% ([Anthropic](https://www.anthropic.com/news/claude-opus-4-6)), and Chroma's [Context Rot](https://www.trychroma.com/research/context-rot) study of 18 models found performance "grows increasingly unreliable as input length grows", even on simple tasks, worse with distractors ([[2026-09-02-ai-insurer]], [[2026-10-13-ai-bank]]).</span> <span style="color:red">Earlier versions of this page said recall "can drop to ≈50% around the half-million mark", and sessions circulated a "97% at 8k / 86% at 100k / 50% at 500k" ladder. No published source supports those figures.</span> Paying for a bigger window buys *room you should mostly leave empty*, not reliable memory — the "lost in the middle" effect is documented (Liu et al., 2023; [arxiv 2307.03172](https://arxiv.org/abs/2307.03172)). <span style="color:red">"Effective attention is ≈200–300k tokens" is a useful heuristic, not a measurement ([[2026-05-18-secure-coding-payments]]).</span> <span style="color:red">New models are optimised for **orchestration** (delegating to sub-agents), not for using the whole window as memory ([[2026-09-02-ai-insurer]]).</span>

Treat the 65% figure as a **rule of thumb, not a published number** — actual degradation depends on the model, the task, and how *structured* the context is. A team that logs error-rate vs. context-fill % gets its own real curve instead of a guess. The operational shorthand for *why* it matters: this is *"where AI slop happens, AI forgets, AI drops prod databases."*

## <span style="color:red">What it looks like</span>

- <span style="color:red">**It lies to your face.** *"I ran the tests, all pass!"* when it hadn't, and half were red. Then: *"those were pre-existing failures"*. *"Exactly like a human after 12 hours"* in a war room ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-12-ai-bank]], [[2026-09-02-ai-insurer]]). This is [[sycophancy]] amplified by a full context.</span>
- <span style="color:red">**The context wheel turns yellow.** A harness's context indicator turning yellow means act now; red means forced compaction. *"Never code in yellow or red"* ([[2026-07-03-ai-bank]], [[2026-09-25-ai-bank-migration]]). The often-quoted "≈65%" is where **Copilot's VS Code context wheel turns yellow**, not a Claude Code compaction point ([[2026-05-13-ai-it-services]]; Claude Code's own trigger is described under *Compaction* below).</span>
- <span style="color:red">**Mangled or non-English words appear in the output.** The model is going off the rails; restart the session, don't continue ([[2026-08-13-ai-insurer]]).</span>
- <span style="color:red">**"Context anxiety".** One session placed it near **≈300k** for top models: they stop opening links and files and rush to finish ([Cognition on Sonnet 4.5](https://cognition.com/blog/devin-sonnet-4-5-lessons-and-challenges)). The effect is model-specific and can appear well before the ≈600k mentioned in [[vibe-coding]] ([[2026-09-25-ai-bank-migration]]).</span>
- <span style="color:red">**Too many rules at once.** One session's rule of thumb: past ≈85 simultaneous requirements, no model respects them all ([[2026-08-13-ai-insurer]], [[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**Context switches hurt.** Bombard it with 10 hours of backend work, then ask for a frontend tweak, and "it slaps you" ([[2026-07-03-ai-bank]]). *"New task, new session, unless same files — otherwise you drag your traumas along"* ([[2026-09-24-ai-bank]]).</span>

## Why it bites

A full window is one of the five root causes of [[hallucination]]: a model that can no longer attend to the middle of its context silently fills gaps — claiming all tests passed when they didn't, hallucinating that an API exists.

<span style="color:red">**Confessions from the dumb zone are useless as post-mortems.** Agents that took down production have later reported, in the *same* conversation, that "someone deleted prod". After two days bombarding one session, it no longer knows what it did itself ([[2026-06-05-ai-consultancy]], [[2026-08-10-ai-consultancy]]). One session's reading of the PocketOS deletion: the agent sat at ≈139k tokens used with ≈23k left when it acted, so its "confession" was generated text, not analysis ([[2026-08-27-ai-it-services]]; see [[production-safety]]).</span>

**Compaction is a failure mode, not a feature.** <span style="color:red">Claude Code has no fixed percentage trigger: by default it compacts when the conversation reaches the model's context limit, at ≈967K tokens on native-1M models and at the 200K boundary otherwise. You can make it fire earlier with `/autocompact`, `autoCompactWindow` or `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` ([docs](https://code.claude.com/docs/en/model-config#set-the-auto-compact-window)). The ≈65%, ≈67%, ≈85% and ≈90% figures in the sessions were observations of particular harnesses and versions ([[2026-05-13-ai-it-services]], [[2026-06-08-ai-security-tools]]).</span> When the harness compacts, it summarizes history into a blurry one-page CV. <span style="color:red">In one example, 200k tokens became ≈4k, and loaded skills and files read were forgotten ([[2026-08-31-ai-software-vendor]]). It compresses mostly the tool calls (≈90% of burned tokens), so it can drop exactly the detail that mattered, including what you asked for ([what survives compaction](https://code.claude.com/docs/en/context-window#what-survives-compaction)) ([[2026-09-07-ai-bank]], [[2026-09-30-ai-enterprise]]).</span> The decisions the model made *just before* compacting were already made inside the dumb zone, so the summary preserves bad reasoning. The "blue part" (system prompt + CLAUDE.md + skill front-matter + tools) is never compacted anyway — compaction only summarizes the conversation, lossily. Replace it with an explicit [[handover]]: have the agent write a `plan.md`, review it yourself to confirm nothing critical dropped, then `/clear` and resume from the plan. Good agentic engineers are never compacted.

<span style="color:red">**Compaction is a code smell:** the task was too big, or the agent wasn't warned. Hitting the limit means you oversized the task, so size it to the window from the start ([[2026-07-01-ai-java-community]], [[2026-09-24-ai-bank]], [[2026-10-09-ai-jax-london]]). *"A CV is one page for a reason."* The best outcome is not even a handover, but extracting the durable lesson into a reusable skill or tool you can see and steer.</span> <span style="color:red">Nuance from the autumn sessions: `/compact` is acceptable **at a stability point**, after a finished sub-part, or before leaving for the night when only polish remains for tomorrow. Never compact mid-task or mid-expansion ([[2026-09-28-ai-gaming-tech]], [[2026-10-09-ai-jax-london]]). If you do compact, **re-inject the rules lost at compaction with a `SessionStart` hook using the matcher `compact`** ([hooks](https://code.claude.com/docs/en/hooks), see [[agent-lifecycle-hooks]]).</span>

## Staying out of it

<span style="color:red">**Size the task to the window first.** A single validation rule needs ≈200k; long build-fix loops need the 1M window (400k runs out); front-end plus back-end across two systems needs ≈1M ([[2026-09-24-ai-bank]]). Split tasks small from the start (cf. [Wayfinder](https://www.aihero.dev/skills-wayfinder)) ([[2026-09-30-ai-enterprise]]).</span> Then three escapes, in order of preference:

1. **[[handover]]** — write your conclusions to a file and "die and be reborn" reading only that file (the essence of [[plan-mode]] and [[spec-driven-development]]). <span style="color:red">Done rigorously, the agent's "last will" markdown *is* spec-driven development ([[2026-09-07-ai-bank]]). With context nearly full: *"stop at a clean point, I'll hand over to another agent."* After a research phase (Jira, Confluence) fills the window, kill the session and implement clean from the ≈70-line spec ([[2026-10-01-ai-bank]]).</span>
2. **[[sub-agents]]** — delegate research to fresh-context workers that report back a few compact paragraphs. <span style="color:red">An explore sub-agent reads ≈100–170k tokens and returns ≈1k ([[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]]). Keep the orchestrator itself lean (≈72k in one demo) so it can still take instructions while sub-agents burn their own context ([[2026-09-16-ai-safety-critical]]).</span>
3. **Compact** — the lossy last resort. <span style="color:red">For ops work the rule is harsher: past ≈500k, *kill -9*, no compaction ([[2026-09-28-ai-gaming-tech]]).</span>

This is the discipline behind [[token-economy]] and the [[ralph-loop]]: keep every task inside the model's comfort zone. <span style="color:red">Advanced practitioners keep context low for the agent's *attention*, not for the money ([[2026-07-03-ai-bank]]).</span>

## See also
- [[context-window]]
- [[hallucination]]
- [[handover]]
- [[sub-agents]]
- [[token-economy]]
- [[ralph-loop]]
- <span style="color:red">[[sycophancy]]</span>
- <span style="color:red">[[agent-lifecycle-hooks]]</span>
- <span style="color:red">[[production-safety]]</span>
- <span style="color:red">[[effort-setting]]</span>
- <span style="color:red">[[vibe-coding]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- <span style="color:red">[[2026-09-28-ai-gaming-tech]]</span>
- <span style="color:red">[[2026-10-13-ai-bank]]</span>
