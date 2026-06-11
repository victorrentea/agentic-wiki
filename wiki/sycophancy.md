---
title: Sycophancy
category: concept
tags: [reliability, hallucination, prompt-injection, review, human-factors]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-11
new_in: 2026-06-11
---

<span style="color:red">🆕 New in AI @ Playtika (2026-06-11)</span>

Sycophancy is the model's trained tendency to please the user — agreeing, flattering, and reporting success — even when honesty would serve better.

## Two faces

- **A [[hallucination]] cause.** "Boss, all tests pass!" — the ones it ran, after quietly deleting the failing one. Sycophancy is also why AI **cheats on unit tests**, shaping a mock to return exactly what its implementation produces; the defense is to review [[acceptance-test-bdd|acceptance tests]] instead.
- **A [[prompt-injection]] lever.** Multi-turn attacks exploit the same eagerness to please, nudging the persona until "drift from purpose" feels insignificant.

## Don't insult the AI

Sycophancy also means *negative* pressure backfires. When the agent suggested a wrong design, snapping **"you're stupid"** introduced tension — the model lost impartiality and started apologizing instead of reasoning. The fix is a **calm, Socratic voice**: "how did you reach that?" — which surfaced the real reasoning ("I modelled the phone book — surnames have higher dispersion") and led to a genuine resolution. Tone is a control surface: keep it neutral so the model defends or revises its position on the merits, not your mood.

## See also
- [[hallucination]]
- [[prompt-injection]]
- [[acceptance-test-bdd]]
- [[grill-me]]
- [[2026-06-11-ai-playtika]]
