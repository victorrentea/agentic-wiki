---
title: Grilling in Reverse
category: pattern
tags: [skill-erosion, learning, code-review, comprehension, grill-me]
sources: ["[[2026-06-26-ai-agentic-how]]"]
created: 2026-06-26
updated: 2026-06-26
new_in: 2026-06-26
---

<span style="color:red">🆕 New in AI@agentic.how (2026-06-26)</span>

Grilling in reverse is the practice of having the AI interview *you* on what existing code does — testing your comprehension rather than having you test the AI's understanding of a requirement — a countermeasure to [[skill-erosion]] and [[cognitive-debt]].

## The pattern

Where [[grill-me]] points the AI at a *spec* or *requirement* to surface hidden assumptions, grilling in reverse points the AI at *existing code* and asks it to interrogate your understanding:

> "Read this module. Then Q&A me on what it does. One question at a time. If I can't answer, tell me the answer."

The AI becomes a tutor, not a code generator. A session of 10–15 questions over a module you haven't read closely reveals exactly which decisions you rubber-stamped during review — which is also exactly where the team's [[cognitive-debt]] accumulates.

## When to use it

- **After AI-generated code lands in a PR** — run it before approving to verify you can answer "why is this designed this way?".
- **Sprint retro** — pair with the [[sprint-design-quiz]] (automated) and manual grilling-in-reverse sessions to ensure team-wide comprehension.
- **Legacy onboarding** — the fastest way to understand a module you've never touched is to let the AI read it and interrogate you until you understand the design decisions.
- **After a sprint with high AI-code volume** — "we shipped a lot; do I still understand the system?" is a healthy check.

## Relation to [[grill-me]]

[[grill-me]] uses AI to stress-test *your design* before you commit to building it. Grilling in reverse uses AI to stress-test *your understanding* of what was already built. Both are one-question-at-a-time dialogues, but in opposite directions:

- Grill me → you answer to refine the spec.
- Grilling in reverse → you answer to prove you understood the code.

The "brilliant!" reaction from practitioners: it is the same conversational format repurposed for education rather than design.

## See also
- [[skill-erosion]]
- [[cognitive-debt]]
- [[grill-me]]
- [[sprint-design-quiz]]
- [[code-review]]
- [[mob-spec-review]]
- [[2026-06-26-ai-agentic-how]]
