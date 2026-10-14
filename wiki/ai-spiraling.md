---
title: AI Spiraling
category: concept
tags: [anti-pattern, debugging, context, sycophancy, token-economy, human-factors]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-05-04-ai-telecom]]", "[[2026-04-07-ai-product-team]]", "[[2026-07-01-ai-java-community]]", "[[2026-05-06-ai-ecommerce]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

AI spiraling is the failure mode where an agent loops on a wrong path — thinking for 10–30 minutes, retrying fix after fix — without measurable progress, burning tokens and time.

## What it looks like

The term was coined by a workshop participant ([[2026-04-16-ai-safety-critical]]). The classic trigger is **missing context**: asked to port a C# decryption method to Java without all the relevant files or the library's spec, the agent "thinks" for 10 minutes and nothing moves. A softer variant is drift in a long conversation: "do this" / "of course" with no change in the code — the [[dumb-zone]] seen live in a long single [[kiro]] spec conversation ([[2026-05-06-ai-ecommerce]]).

The human reflex makes it worse. Each retry feels like "one more coin in the slot" — the same variable-reward loop as [[ai-addiction]] — and `/clear`-and-replay the same prompt is roulette, not debugging ([[2026-07-01-ai-java-community]]).

## The stop rule

Interrupt and redirect when there is **no measurable progress after ≈3 tool calls in the same direction**, or after **3 fixes that didn't work**. The second threshold is the iron law of the `systematic-debugging` skill in [[superpowers]]: no more fixes without a root cause.

## Recovery recipe

From [[2026-05-04-ai-telecom]]:

1. **Stop** the agent.
2. Ask calmly: *"How did you get here? Walk me through your reasoning."* **Not** "you're wrong" — that triggers [[sycophancy]] ("yes master, I was wrong") instead of thought. *"You don't tell it 'you're wrong'… you lay it on the couch and ask, sweetie, what was going on in your head?"*
3. Find the wrong assumption in its chain — the "sign that said 42" it followed.
4. Identify the knowledge it was missing.
5. `/clear` and start a new conversation with that knowledge included (see [[handover]]).
6. Add a rule to [[claude-md]] so it can't recur ([[agents-md-discipline]]).

Cheaper shortcut for steps 2–4: ask *"what else would you need?"* and hand it the missing files — or, in agent mode, let it read the codebase itself. Before re-running anything, first try **increasing the precision of the prompt**.

## Prevention

- **Slow it down before risky refactors**: *"who worked here before?"* forces the agent to read `git blame` and history and realise the code has a complicated past ([[2026-05-04-ai-telecom]]).
- Give context up front rather than mid-spiral — specs, files, examples ([[spec-driven-development]], [[example-driven-prompting]]).
- Keep conversations short: `/clear` between phases so stale context doesn't drag the agent into [[hallucination]].
- Each spiral costs real money — see [[token-economy]].

## See also
- [[sycophancy]]
- [[superpowers]]
- [[dumb-zone]]
- [[handover]]
- [[agents-md-discipline]]
- [[hallucination]]
- [[ai-addiction]]
- [[token-economy]]
- [[2026-04-16-ai-safety-critical]]
- [[2026-05-04-ai-telecom]]
