---
title: Plan Mode
category: pattern
tags: [planning, claude-code, deep-thinking, context-management, single-threaded, task-sizing]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

Plan mode (Shift-Tab in Claude Code) is a read-only thinking phase where the agent brainstorms and designs an approach *before* touching any code — single-threaded deep thinking that must not be parallelized.

## Single-threaded by design

The hard rule: **don't open a second terminal while in plan mode.** Humans cannot run two deep-thinking tasks in parallel — one terminal does the hard thinking; the others may do shallow polishing at most. (Napoleon could famously do three things at once on his horse — none of them deep thinking.) Violating this is the gateway to the [[ai-addiction]] dopamine trap of four idle terminals draining tokens and attention.

<span style="color:red">**Warm up before you enter.** *"You can't fight a 40-year architect cold"* with a post-meeting brain: chat about the problem first, and come to Opus with five questions you thought of yourself, not *"let's go"* ([[2026-08-12-ai-bank]], [[2026-09-14-ai-industrial]]). The caveat on speed: three days of thinking compress into two hours of interview and review — but hard decisions are exactly the ones you **don't** want on fast-forward.</span>

## Where it fits

<span style="color:red">Pick the weapon by how **unknown** the task is, not only by its size. The early three-tier table (prompt → plan mode up to a day → spec for multi-day) evolved through several slightly different ladders over the summer; by autumn 2026 it was a five-rung ladder ([[2026-09-14-ai-industrial]], [[2026-09-24-ai-bank]]):</span>

| <span style="color:red">Situation</span> | <span style="color:red">Weapon</span> |
|---|---|
| <span style="color:red">≤ half a day, you know the files</span> | <span style="color:red">**Just prompt** — autopilot, no ceremony, then check</span> |
| <span style="color:red">Massive or hostile legacy ("where people died")</span> | <span style="color:red">**Scouts first** — [Explore / research sub-agents](https://code.claude.com/docs/en/common-workflows#delegate-research-to-subagents), or Opus burns 500k tokens reading</span> |
| <span style="color:red">Unknown territory, ≈1–4 days</span> | <span style="color:red">**Plan mode**, plus an interview if the plan comes out "bloodless"</span> |
| <span style="color:red">You don't even know which questions to ask</span> | <span style="color:red">**An interview skill** — [[grill-me]]</span> |
| <span style="color:red">Unknown and multi-day (≥2 days–1 week)</span> | <span style="color:red">**[[spec-driven-development]]** — markdown on disk ([[openspec]]); only ≈1 task in 5 earns it</span> |

<span style="color:red">Variants worth knowing: one session put the spec threshold at ≥1 week ("sleep on the idea", a remote colleague reviews overnight) and plan mode at 2–4 days or new terrain such as a first paginated grid ([[2026-08-31-ai-software-vendor]]); another made plan mode **mandatory on any task above ≈2 h of manual work** ([[2026-08-06-ai-bank]]). A rule of thumb from May: *"any task that would have taken at least a day last year → spec it; any task you'd have a hard time filling a timesheet for → vibe it"* — and a good harness notices feature creep mid-vibe (*"Boss, this is no longer vibing — do you want to spec?"*). **Unfamiliar tech** (a first Kafka integration) calls for a **spike**, not a brainstorm ([[tracer-bullets]]). Always keep a back door for a direct prompt; on a trivial task, use the whip; and if the 8-hour task took 1 hour, reinvest the hours in understanding what was written.</span>

Plan mode is the middle tier — Shift-Tab Shift-Tab activates it in Claude Code. <span style="color:red">Among the IDE-plugin modes, **Ask** is slavery (you copy, you compile — only to learn or when out of quota), **Agent** is the default, **Plan** is read-only ([[harness]]).</span>

The output of a plan-mode session is a [[handover]] — the distilled conclusions you restart from, keeping the agent out of the [[dumb-zone]]. <span style="color:red">Pair it with [[prompt-caching]] awareness: deep thinking is slow, and while a Claude subscription's main conversation now gets a 1-hour cache, sub-agents — and everything on an API key — get only 5 minutes ([docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets)), so a long pause can still mean a full-price cache rebuild.</span> <span style="color:red">A yellow context ring during the task means the task was too big ([[context-window]]).</span>

This three-tier framing came from a concrete observation: plan mode is *"too ephemeral"* for multi-day work — you can't share it, hand it off, or review it asynchronously. <span style="color:red">The plan isn't written to disk: once approved, the agent implements — the contrast with SDD's persistent files ([[2026-05-27-ai-open-workshop]]).</span> [[spec-driven-development]] with [[openspec]] is the persistent, reviewable answer for the third tier. The mapping also clarifies when *not* to use plan mode: if a task fits in a few hours and you know the files, just prompt directly and save the planning overhead.

## <span style="color:red">Getting a good plan</span>

- <span style="color:red">**The prompt-quality ladder for a big ticket:** ❌ *"I want search on columns"* (guaranteed slop) → ⚠️ *"implement issue #24"* (at least points at a ticket a human thought about) → ✅ `/plan implement GitHub issue #24` — read-only, reads code and ticket, asks you questions, outputs an implementation markdown. Split an oversized ticket first (search vs sort+paginate) into separate issues with `gh` ([[2026-05-13-ai-it-services]]).</span>
- <span style="color:red">**Push on the plan.** For "remove the phone column" the naive plan deleted the `<th>`/`<td>`; asked *"what about the backend — are we still bringing the same data?"*, the agent found the Owner DTO was reused in 3 other screens and correctly backed off ([[2026-05-27-ai-open-workshop]]).</span>
- <span style="color:red">**Plan mode asks few questions** — ≈3–7; a grill prompt surfaces 10+ and up to ≈35. If the plan has no questions for you, it's guessing ([[grill-me]]).</span>
- <span style="color:red">**Permissions can be loose**: in plan mode you can allow it anything — it proposes, it doesn't execute ([[agent-permissions]]).</span>
- <span style="color:red">**On legacy, warn it of the disaster ahead** — it then sends exploration sub-agents itself. Git history shows where hell is: how many brains wrote in a class, how many bugs were filed against it ([[bug-heatmap]], [[2026-09-16-ai-safety-critical]]).</span>

## <span style="color:red">From plan to implementation</span>

- <span style="color:red">**Isolate contexts on purpose**: architect in one conversation, hand off a markdown, let a cheaper model implement — you pay tokens to buy focus ([[model-hierarchy]], [[sub-agents]]).</span>
- <span style="color:red">**Verbose plans are the cheap side of the trade**: 40 lines of markdown beat scrolling 600 lines of diff ([[code-review]]).</span>
- <span style="color:red">**Fix the template, not just the plan.** A live prompt that moved technicalities out of the proposal: *"…the PO doesn't know what JSON is… move all the technicalities from the proposal into the design markdown… And change the OpenSpec markdowns, so from now on this happens every time"* — followed by checking *"Did you also update the OpenSpec skills like I told you to?"* ([[self-improving-knowledge]]).</span>

## See also
- [[grill-me]]
- [[handover]]
- [[spec-driven-development]]
- [[dumb-zone]]
- [[ai-addiction]]
- [[prompt-caching]]
- <span style="color:red">[[openspec]]</span>
- <span style="color:red">[[sub-agents]]</span>
- <span style="color:red">[[tracer-bullets]]</span>
- <span style="color:red">[[model-hierarchy]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
