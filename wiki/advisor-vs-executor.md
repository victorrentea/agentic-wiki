---
title: Advisor vs Executor Mode
category: pattern
tags: [workflow, validation, feedback-loop, multi-model, prompting]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-07-30-ai-safety-critical]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

You use an AI either as an **advisor** (it generates options, you judge) or as an **executor** (it does the work *and proves it*), and each mode needs a different way of checking the output.

## The two modes

| | Advisor | Executor |
|---|---|---|
| Typical work | brainstorming, design alternatives, review hints, "where could the bug be?" | `/opsx:apply` ([[openspec]]), *"optimize this endpoint until p95 < 200 ms"*, TDD red→green ([[red-green-tdd]]) |
| Can it prove its work? | No — you are the judge | Yes — it self-validates ≈90%, you spot-check |
| How to raise confidence | **roll the dice again**: 2–3 runs, ideally on different models | tighten the feedback signal, then trust the loop |

*"AI is a dice — different rolls reveal different angles."* ([[2026-04-27-ai-healthcare-software]])

## The tell-tale: a measurable signal

You are in executor mode only if the prompt contains a **measurable [[feedback-loop|feedback signal]]**: a test name, a latency target, a lint rule, a schema to match, a green build. If you can't name the signal, you are really in advisor mode — whatever the prompt says — and should treat the output as a suggestion to verify, not a result.

The mindset shift for the day is to move AI work from the first column to the second: from "it guesses things for me to try" to "it can prove its work". In one session, hours of "find the bug" with a weak model went nowhere; a stronger model given *"reproduce JIRA-1234 in the browser, write a failing test, fix, push"* succeeded — the difference was as much the provable loop as the model.

## Advisor mode: diversity over repetition

- Run the same brainstorm on two models: outputs typically overlap ≈60–70%, and the **≈30–40% delta is the thinking you didn't have**. This is the cheap end of [[multi-model-review]].
- Model quality ≠ requirement coverage: in one exercise the cheaper model got the diacritics requirement right while the stronger one silently skipped it — another reason to dice-roll advisor output rather than trust the "best" model.
- A **cross-family advisor** consulted during coding (e.g. a GPT model advising a Claude executor) brings opinions free of the executor's own decision bias — the executor tends to defend choices it already made ([[2026-07-30-ai-safety-critical]]). The room's shorthand: *"when Santa Claude is sick, you go to Geppetto."*

## Executor mode: invest in the loop

Executor quality comes from the harness, not the prompt: tests the agent can run ([[ci-green-loop]]), a browser it can drive ([[browser-mcp]]), a measurable target. Once the loop exists, review shifts from reading every line to spot-checking and [[grill-me|grilling]] the result.

Not to be confused with the Spring AI [[advisor]] — a middleware interceptor around an LLM call.

## See also
- [[feedback-loop]]
- [[multi-model-review]]
- [[model-hierarchy]]
- [[grill-me]]
- [[superpowers]]
- [[red-green-tdd]]
- [[advisor]]
- [[2026-04-27-ai-healthcare-software]]
