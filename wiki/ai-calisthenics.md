---
title: AI Calisthenics
category: pattern
tags: [human-factors, learning, reconversion, practice, habits, parallel-agents]
sources: ["[[2026-07-01-ai-java-community]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

AI calisthenics is a ladder of deliberately uncomfortable, self-imposed constraints that force a developer to re-train as an agentic engineer — the AI-era analogue of object calisthenics.

## Why drills, not advice

Knowing that you *should* let the agent do the work does not change habits; the hand goes back to the keyboard the moment a change looks "faster to do myself". The calisthenics framing borrows from object calisthenics: rules that are too strict for daily production use, practised for a while precisely because they hurt a bit, until the new reflex sticks ([[2026-10-07-agentic-reconversion-devoxx]]). It is the practical half of [[agentic-reconversion]] and the counterweight to [[skill-erosion]]: you exercise judgement, specification and review instead of typing.

## The ladder

Roughly from easiest to hardest, as taught across the 2026 sessions ([[2026-07-09-ai-live-casino]], [[2026-07-22-ai-agentic-how]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]):

1. **Start with the problem, not an order.** Describe what hurts, like at the psychologist — or the confessional: *"Father, I have sinned"* — and let the agent propose the fix. Give it the **problem, not the decision**: it comes back with options and pros/cons. This is the antidote to [[answer-injection]] and the first drill taught ([[2026-09-25-ai-bank-migration]], [[2026-10-07-agentic-reconversion-devoxx]]).
2. **Touch no file by hand.** No typing into project files — a single manual extract-method counts as a fail. Fix things by prompting, or by improving the instruction that caused the mistake ([[2026-07-01-ai-java-community]], [[2026-09-30-ai-enterprise]]).
3. **Zero paste — Ctrl+V means guilt.** Never paste logs, rows or tickets into the agent; connect it to the source and let it fetch its own data. One drill: give it a Postgres MCP with a rate limit and only 3 allowed queries instead of free SQL, so it has to think about what to ask. See [[chop-ai-waiter]] for the four ways to connect.
4. **Pretend the IDE doesn't exist.** Start each task in the agent CLI; then go further — a whole workday with only the CLI open and no other program ([[2026-07-01-ai-java-community]] cites this as a challenge from an Anthropic lead).
5. **Dictate a whole day.** Every prompt by voice ([[wispr-flow]] or another dictation tool).
6. **Multiplex 4 parallel conversations, none idle for more than 5 minutes.** Split the work into independent zones, one [[worktree]] each, and keep all four busy; a scoring hook can turn it into whack-a-mole ([[2026-08-31-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]]). Treat this strictly as a *drill*: deep work stays single-threaded (see [[cognitive-debt]], [[ai-addiction]]).
7. **Leave the desk.** [Code from your phone](https://code.claude.com/docs/en/remote-control), or converse with an agent over email while commuting (e.g. via an agent inbox such as [AgentMail](https://agentmail.to)) ([[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]).

Two more moves sit outside the ladder:
- **Vibe-code in a language you can't read fluently** — 1–2 months, or a full-vibe passion project, generating Swift or Python as a Java developer. You can't hand-edit, so you are forced to judge *outcomes*, not code aesthetics ([[2026-07-20-ai-telecom]]; also in [[skill-erosion]]).
- **Spend a day next to an "AI champion"** in your company and copy their reflexes ([[2026-09-02-ai-insurer]]).

One claim used to motivate the first rungs: generating >90% of your code through prompts already puts you, by the presenter's estimate, among the top ≈5% of developers worldwide ([[2026-08-31-ai-software-vendor]]) — a rough heuristic, not a survey figure.

## The ceiling

The point is throughput *and* sanity. The stated ceiling is about [4 terminals × 4 hours](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163) a day — Steve Yegge's "AI vampire" warning — which by then can be a week's worth of old-style output; the rest of the day is work-life balance ([[2026-08-31-ai-software-vendor]]). Past that, [[system-2-fatigue]] wins.

## Open question

The sessions left one debate unresolved: *"we are the last generation that wrote code by hand"* versus Karpathy's counter that newcomers who never typed code will simply be fitter in this new sport ([[2026-09-02-ai-insurer]]).

## See also
- [[agentic-reconversion]]
- [[skill-erosion]]
- [[answer-injection]]
- [[chop-ai-waiter]]
- [[wispr-flow]]
- [[worktree]]
- [[ai-addiction]]
- [[cognitive-debt]]
- [[system-2-fatigue]]
- [[agentic-engineering]]
