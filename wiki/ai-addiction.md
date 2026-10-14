---
title: AI Addiction (The Dopamine Trap)
category: concept
tags: [human-factors, attention, parallel-agents, burnout, anti-pattern]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-14-ai-industrial]]"]
created: 2026-06-11
updated: 2026-10-13
---

AI addiction is the dopamine/gambling loop of agentic work — spinning up parallel terminals chasing the next spectacular result — that drains your tokens and your agency, often by 1pm.

## The slot-machine loop

Bored mid-build, you start task 2 in a new terminal, then task 3, and go home **drained of agency and tokens** ("Boss, I'm done for the month"). People hit "done for the month" by 1pm running four parallel terminals, brain-dead. The structure is literally slot-machine psychology — a **variable-reward loop** where the occasional spectacular output "wipes the shit" of the boring ones and keeps you pulling the lever. "This gives dopamine hits — it's addictive, very dangerous."

<span style="color:red">"Agentic coding = addiction engineering": "it took away the selfish pleasure of coding but gave me the most addictive game I ever played." ([[2026-07-09-ai-live-casino]])</span>

## The new shape of a working day

<span style="color:red">A feature that took ≈5 days in 2025 now takes ≈4–6 hours: heavy up-front discussion and markdown review ("this is where the brain bleeds"), fire the agent off for hours, watch for 5 minutes, jump to terminal #3. A week of 2022-style thinking (grooming, 2–3 deep-focus blocks a day) is compressed into one morning — which is why you are wrecked by 1pm and lose willpower and attention to detail on the polish tasks that follow ([[2026-07-01-ai-java-community]], [[2026-07-09-ai-live-casino]]). The downstream risk is burnout plus LGTM-slop from over-parallelizing — see [[cognitive-debt]] and [[approval-fatigue]].</span>

## Agency depletion and "AI psychosis"

<span style="color:red">**Agency depletion** is Victor's term for AI burnout: the developer after 8 hours with four parallel agents, melted on the couch, unable to answer "what do we do for the holidays?" — "back home you sit like a plant". "I lost 4 kg in a month doing this." ([[2026-04-27-ai-healthcare-software]], [[2026-07-01-ai-java-community]]) See [[system-2-fatigue]].</span>

<span style="color:red">**"AI psychosis"** is a mania-like phase almost everyone passes through on first using Claude Code intensely — typically right after the first day running two terminals at once: not sleeping ("five more features before morning"), shame at leaving agents idle, 2–3 months without proper sleep. Karpathy and Simon Willison talk about it openly. Show-of-hands across 2026 conferences: ≈20 people in one room, about half of a 300-person room, about a quarter of a Devoxx London room. Almost every developer has lost a weekend letting Opus run 8 hours on one prompt. Advice from the room: "Sleep, drink water." ([[2026-05-04-ai-telecom]], [[2026-05-13-ai-it-services]], [[2026-07-20-ai-telecom]])</span>

## Budgets that hold

<span style="color:red">The numbers that keep recurring across sessions:</span>

- <span style="color:red">**Max 2 agents in parallel, max 1 doing deep thinking**; the other(s) only on polish — UX tweaks, docs, comments, research ([[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">**≈3–4 hours of multi-agent work per day** before the brain is dust — Steve Yegge, [The AI Vampire](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163): "at 12 I close the laptop". Beyond ≈3 h/day of juggling, "vegetable mode" ([[2026-08-31-ai-software-vendor]], [[2026-04-27-ai-healthcare-software]]).</span>
- <span style="color:red">Single-agent windows of ≈90 min, then a hard 15-minute break.</span>
- <span style="color:red">**One orchestrator spawning N [[sub-agents]] is fine** — you talk to one boss, not N.</span>
- <span style="color:red">Juggling 2–4 terminals is OK only if all but one do light work; colour each terminal by a hash of its folder so you know where you are ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">"4 parallel terminals = ping-pong with 4 paddles": split the task into 4 independent zones and keep all 4 busy. A rumoured Silicon Valley interview format gives a candidate 4 terminals, unlimited tokens and one hour, and watches how they multiplex. Treat this as a *drill* ([[ai-calisthenics]]), not as how deep work is done ([[2026-07-20-ai-telecom]]).</span>

## Guarding against it

The defenses are about deliberately protecting attention:

- **[[plan-mode]] is single-threaded** — never run two deep-thinking tasks at once.
- <span style="color:red">Idle parallel terminals are also a [[prompt-caching]] burn: a fat context left past the cache TTL is re-written at full price on the next turn. On a Pro/Max subscription the main conversation's cache lives 1 hour, but subagents and API-key sessions get only 5 minutes ([Claude Code docs](https://code.claude.com/docs/en/prompt-caching#which-ttl-each-request-gets)). They are also a [[dumb-zone]] hazard.</span>
- Use [[worktree|worktrees]] to make *whatever* parallelism you do at least technically safe.
- <span style="color:red">Treat verbose agent narration as another attention drain; a terse-output skill like [[caveman]] trims it (JetBrains measured ≈8.5% fewer output tokens, not the advertised 65% — [source](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/)).</span>
- <span style="color:red">**Don't fire seven terminals and go on a break — "ride the agent"**: check every ≈3 minutes which tools it uses and which path it takes, or you'll find it stopped halfway. Put a `Stop` hook bell on turn end and on permission requests ([[agent-lifecycle-hooks]], [[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**"It's a skill to say NO to an idea"**: over-automation is waste. A tool that screenshots your screen every 5–10 minutes shows where you *really* lose time ([[2026-07-09-ai-live-casino]]).</span>

## See also
- [[plan-mode]]
- [[prompt-caching]]
- [[dumb-zone]]
- [[worktree]]
- [[caveman]]
- <span style="color:red">[[system-2-fatigue]]</span>
- <span style="color:red">[[cognitive-debt]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[ai-calisthenics]]</span>
- <span style="color:red">[[sub-agents]]</span>
- [[2026-06-11-ai-mobile-gaming]]
