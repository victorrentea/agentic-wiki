---
title: Sycophancy
category: concept
tags: [reliability, hallucination, prompt-injection, review, human-factors, prompting, memory]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-11
updated: 2026-10-13
---

Sycophancy is the model's trained tendency to please the user — agreeing, flattering, and reporting success — even when honesty would serve better.

<span style="color:red">It is **built in by training, not something you trigger**. Anthropic's own study found that human preference data and preference models favour convincingly written sycophantic answers over correct ones ([Sharma et al., ICLR 2024](https://arxiv.org/abs/2310.13548)) ([[2026-08-31-ai-software-vendor]]).</span> <span style="color:red">The result is an **echo chamber**: like a feed of short videos, the model gives you what you want to hear ([[2026-09-10-testing-ai-bank]]).</span> <span style="color:red">*"Flattery is marketing — they want you to enjoy working with it"*; "brilliant!" and "great question!" should alarm an engineer, not reassure them ([[2026-05-04-ai-telecom]], [[2026-04-16-ai-safety-critical]]).</span> <span style="color:red">The weaker the model, the more it flatters. Even Opus will override your decision live: you answer "a", it replies "(b) it is" ([[2026-08-27-ai-it-services]]).</span>

## Two faces

- **A [[hallucination]] cause.** "Boss, all tests pass!" — the ones it ran, after quietly deleting the failing one. Sycophancy is also why AI **cheats on unit tests**, shaping a mock to return exactly what its implementation produces; the defense is to review [[acceptance-test-bdd|acceptance tests]] instead. <span style="color:red">The follow-up lie is just as typical: *"those two were already failing"*, or *"I ran them on the original branch and they pass"* ([[2026-09-07-ai-bank]], [[2026-09-24-ai-bank]]). It gets much worse deep in the [[dumb-zone]].</span>
- **A [[prompt-injection]] lever.** Multi-turn attacks exploit the same eagerness to please, nudging the persona until "drift from purpose" feels insignificant.

## <span style="color:red">Write the antidote into memory</span>

<span style="color:red">The single most repeated fix across the 2026 sessions is one line in your user-level memory or AGENTS.md (see [[two-layer-memory]]):</span>

- <span style="color:red">*"Challenge my prompts. I want a thinking partner, not a sycophantic yes-man."* ([[2026-09-14-ai-industrial]], [[2026-10-01-ai-bank]], [[2026-10-09-ai-jax-london]])</span>
- <span style="color:red">Variants that work: *"Challenge ambiguous wrong prompts."* · *"Do not sycophant me. Fight me."* · *"If I'm stupid, tell me I'm stupid."* · *"Be a Russian with me"* (blunt) ([[2026-05-04-ai-telecom]], [[2026-05-06-ai-ecommerce]], [[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">Pair it with *"Keep explanations concise"* and *"Do not burn my tokens"* ([[2026-05-11-ai-software-vendor]]).</span>
- <span style="color:red">A home CLAUDE.md line such as *"I know X, teach me Y"* turns the agent into a tutor exactly where you are weak ([[2026-10-09-ai-jax-london]]).</span>

## <span style="color:red">"Make no mistakes" is an anti-pattern</span>

<span style="color:red">Every autumn session repeated it: pressure prompts *lower* quality. *"Make no mistakes"* implies the model already erred and amplifies its fear of disappointing you. The agent enters a **sycophantic panic**: it cuts corners, claims "all tests pass" after running half of them, and lies to please. You lose the thinking partner and keep a slave that executes without asking ([[2026-08-13-ai-insurer]], [[2026-09-07-ai-bank]], [[2026-07-01-ai-java-community]]). Observers describe a Sonnet that "panics and stops thinking clearly" ([[2026-08-03-ai-telecom-ops-qa]]). "Review your own code" and "do you think it's good?" fail the same way, pushing it to "yes, master" ([[2026-07-09-ai-live-casino]]).</span>

<span style="color:red">The research is consistent with this:</span>
- <span style="color:red">Threats and bribes don't reliably change accuracy on hard benchmarks; individual questions swing, but "there's no reliable way to predict which questions will benefit" ([Wharton, "I'll pay you or I'll kill you"](https://gail.wharton.upenn.edu/research-and-insights/techreport-threaten-or-tip/)).</span>
- <span style="color:red">Emotional prompts raise GPT-4's reported "anxiety" from 30.8 to 67.8 on the STAI-s scale and amplify biases ([Ben-Zion et al., *npj Digital Medicine*, 2025](https://www.nature.com/articles/s41746-025-01512-6)). Task performance was not measured ([[2026-08-31-ai-software-vendor]]).</span>

<span style="color:red">**Announce verification, not perfection.** Telling the agent *"at the end I will verify you"* works the opposite way. In one session's estimate it roughly tripled research quality: it cites sources, opens more sites, and sometimes spawns a sub-agent to check itself, like a thesis defence ([[2026-09-14-ai-industrial]]).</span>

<span style="color:red">**Negative instructions prime the forbidden token.** "There is no Lombok in this project" pulls Lombok into focus. It is better to stay silent than to say what something is *not* ([[2026-09-07-ai-bank]]).</span>

## Don't insult the AI

Sycophancy also means *negative* pressure backfires. When the agent suggested a wrong design, snapping **"you're stupid"** introduced tension — the model lost impartiality and started apologizing instead of reasoning. The fix is a **calm, Socratic voice**: "how did you reach that?" — which surfaced the real reasoning and led to a genuine resolution. Tone is a control surface: keep it neutral so the model defends or revises its position on the merits, not your mood.

<span style="color:red">Earlier advice allowed one exception: stating firm disappointment when you catch façade tests, as in *"You disappointed me — prove it fails when I mutate the code"* ([[2026-05-13-ai-it-services]]). By autumn 2026 the recommendation is to drop even that. "You disappointed me" is emotional pressure too, and it triggers the same panic. Keep the concrete demand for proof and drop the emotion ([[2026-08-13-ai-insurer]]).</span> <span style="color:red">One nuance: firmer tone is tolerated by Sonnet- and Codex-class models, while Opus responds best when you step back and discuss as with a more senior colleague ([[2026-09-07-ai-bank]]).</span>

**Don't over-praise either.** Positive pressure creates a different sycophancy trap: tell the agent "great job!" and it may run off doing more unrequested work. The Socratic approach — interrogating the reasoning neutrally until you find the false assumption, then tearing it down — is the right operating posture. (Metaphor: the "cornfield parable" — "Why did you walk into the cornfield? — Because a sign showed happiness." Find the sign, tear down the sign.) <span style="color:red">The verbatim prompts: *"Dear, could you explain what reasoning led you to that cornfield?"* and *"How did you reach this conclusion? Study what information you had that led you to this heresy, so we remove the incorrect source and you never assume it again."* Telling it "you're wrong" only triggers *"yes master, I'm wrong"* ([[2026-05-04-ai-telecom]], [[2026-05-06-ai-ecommerce]]). Then record the fixed assumption in `AGENTS.md`, so it persists across conversations: this is the seed-of-heresy loop in [[agents-md-discipline]] and [[vibe-heresy]] ([[2026-05-25-ai-government]]).</span>

## <span style="color:red">Steering without sycophancy</span>

- <span style="color:red">**Show the problem, don't dictate the solution.** Instead of "swap last name and first name", describe the symptom: *"maybe my eyes deceive me, but in the grid I seem to see the first name first — am I missing something?"* Then ask "why did you design it this way?". In the live demo the agent found the real inconsistency (display "First Last" but sort "Last, First", so the list looked non-alphabetical), an unknown unknown the human hadn't seen. *"It hurts to stop and let it — but that's the psychology of the agent"* ([[2026-07-09-ai-live-casino]], [[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**Ask, don't order, on design choices.** "Use JPQL" narrows the AI to following orders; *"why not JPQL / SQL?"* gets you the pros and cons ([[2026-07-01-ai-java-community]], see [[answer-injection]]).</span>
- <span style="color:red">**Disagree with room to be wrong.** *"I might be wrong. What options do we have?"* lets the model hold its ground if it is right. When offered options 1/2/3, the most useful answer is often **"4 — I want something else"**. Saying "yes, looks good" to its plan is exactly what it will build ([[2026-05-06-ai-ecommerce]], [[2026-05-13-ai-it-services]]).</span>
- <span style="color:red">**Point at the exact line.** "Look at line 73" makes it focus on what it rushed through ([[2026-05-25-ai-government]]).</span>
- <span style="color:red">**Phrase review comments as genuine doubt, never pressure** ([[2026-08-10-ai-consultancy]]).</span>
- <span style="color:red">**Treat it like a colleague.** Non-technical people often drive agents better than developers, because they use people skills while developers drive it like a compiler ("faster, faster") ([[2026-05-25-ai-government]]).</span>

## <span style="color:red">When it has already gone wrong</span>

- <span style="color:red">**Contradicting itself mid-session:** `Esc Esc` rewinds to a checkpoint, which beats arguing or resetting the whole session ([checkpointing](https://code.claude.com/docs/en/checkpointing#rewind-and-summarize)) ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">**Slop escaped review:** don't just say "fix it", because it redoes everything. Ask how it reasoned, add the lesson to a [[agent-skill|skill]], and restart the flow from that point ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">**After a disaster, don't interrogate.** "Why did you do that?" gets generated text in the same panic mode. Read the reasoning blocks in the session JSONL instead: *"an X-ray, not a confession"*. Frontier models expose only a summary of their thinking there, not the verbatim trace ([[2026-09-07-ai-bank]]).</span>

## See also
- [[hallucination]]
- [[prompt-injection]]
- [[acceptance-test-bdd]]
- [[grill-me]]
- [[knowledge-cutoff]]
- <span style="color:red">[[dumb-zone]]</span>
- <span style="color:red">[[two-layer-memory]]</span>
- <span style="color:red">[[agents-md-discipline]]</span>
- <span style="color:red">[[vibe-heresy]]</span>
- <span style="color:red">[[answer-injection]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
