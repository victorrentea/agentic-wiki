---
title: "System 2 Fatigue: AI Ate System 1"
category: concept
tags: [human-factors, burnout, cognitive-load, review, spec-driven-development, parallel-agents]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

System 2 fatigue is the exhaustion that comes when AI takes over the mechanical "System 1" stretches of coding, leaving engineers in slow, effortful "System 2" thinking all day — the root of fatigue-LGTM and agentic burnout.

## Kahneman's two systems, applied to coding

From Daniel Kahneman's [*Thinking, Fast and Slow*](https://en.wikipedia.org/wiki/Thinking,_Fast_and_Slow): **System 1** is fast and reflexive — closing a brace, autocompleting an import, typing a mapper while humming. **System 2** is slow, deep and tiring — reading a spec, weighing a trade-off, reasoning about a race. *"AI took System 1 from us. We're constantly in deep thinking — that's why we get tired so fast"* ([[2026-04-27-ai-healthcare-software]]).

A 30-year architect sketched the difference:

```
traditional coding   ▓░▓░▓░▓░▓░▓░▓░   deep / mechanical, balanced
spec-driven coding   ▓▓▓▓░░░░░░▓▓▓    deep spec · AI black box · deep review
```

In 2024 muscle-memory typing let the brain rest between deep-thinking bouts; with agents the day is all deep thinking, front-loaded into the spec and back-loaded into review, with no rest in between ([[2026-10-07-agentic-reconversion-devoxx]]).

## How much has been compressed

The sessions keep finding the same ratio from different angles:

- A task that took **5 days in 2025, with System 2 only when needed**, became **4 hours of nearly pure System 2 across 4 agents** in 2026 ([[2026-07-09-ai-live-casino]]).
- Pagination was 3–5 days of work in 2024; today it is ≈6 hours of hard thinking ([[2026-09-30-ai-enterprise]]).
- A week of deep thinking now fits in a two-hour brainstorm ([[2026-09-28-ai-gaming-tech]]).

The names vary — "brain burn", "brain fry", "agency depletion", Steve Yegge's [AI Vampire](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163) ("by noon I'm done") — the mechanism is the same ([[2026-05-27-ai-open-workshop]], [[skill-erosion]]). After a day of up-front System-2 decisions across parallel terminals, *"back home you sit like a plant"* and can't pick up the TV remote ([[2026-07-01-ai-java-community]], [[2026-10-07-agentic-reconversion-devoxx]]).

## Fatigue-LGTM — the risk to quality

The biggest risk of [[spec-driven-development]] is a tired reviewer: you read the markdown, say "looks good", and ship a misaligned feature — especially in modules you're not comfortable in ([[2026-04-27-ai-healthcare-software]]). **Parallel terminals drain your agency, and a dead brain keeps approving** ([[2026-10-07-agentic-reconversion-devoxx]]); see [[approval-fatigue]]. Counters:

- Mob- or pair-review the spec ([[mob-spec-review]]).
- Never review specs at the end of the day — you have a circadian rhythm, the AI doesn't.
- Give reviewers a quota: *"find three things you'd push back on."*

## Budgeting the brain

Rules of thumb from the sessions (heuristics, not research):

- **At most ≈4 hours of hard brain per day** ([[2026-10-01-ai-bank]]).
- **One hard task + one polish task** in parallel, not two deep ones; four terminals means dead by 1 pm ([[2026-09-30-ai-enterprise]]). Don't open a second deep terminal — one participant was running 11 ([[2026-09-28-ai-gaming-tech]]). The stricter limits (≤2 agents, ≤1 deep, ≈90-min windows) are on [[ai-addiction]].
- **Learn ≈2 genuinely new things a day** — about 5 questions inside your sphere and 2 outside, not 50; don't let yourself be machine-gunned with acronyms ([[2026-05-04-ai-telecom]]).
- **Know yourself:** learn your daily brain limit, the signs that you've gone brain-dead, and your fastest detox. Brain-dead is fine for **polishing a screen, never for architecture** — Covey's "sharpen the saw" ([[2026-10-07-agentic-reconversion-devoxx]]).
- **Keep a quest.** Doing good and helping others keeps you sane; alone, AI becomes building for building's sake ([[2026-10-07-agentic-reconversion-devoxx]]).

Re-training your working habits on purpose for agentic work is the theme of [[ai-calisthenics]]; losing the System-1 reps that used to consolidate fundamentals is [[skill-erosion]], and shipping code nobody fully understood is [[cognitive-debt]].

## See also
- [[ai-addiction]]
- [[mob-spec-review]]
- [[approval-fatigue]]
- [[skill-erosion]]
- [[cognitive-debt]]
- [[ai-calisthenics]]
- [[spec-driven-development]]
- [[agentic-reconversion]]
- [[plan-mode]]
