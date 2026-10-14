---
title: Red-Green TDD for Agents
category: pattern
tags: [tdd, testing, feedback-loop, debugging, evidence, playwright, atdd]
sources: ["[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-28-ai-gaming-tech]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Red-green TDD for agents means making the agent prove every change with a test that is **red before and green after** — the deterministic [[feedback-loop]] that turns *"I think it's fixed"* into evidence.

## The failure it cures: premature fixation

Left alone, an agent announces *"Boss, I found the bug!"* like a child, before proving anything — then patches the symptom it guessed at. Without a test it *believes* it's done and moves on ([[hallucination]], [[sycophancy]]). The remedies, in increasing power ([[2026-06-02-ai-open-workshop]]):

- ⭐ make it **explain why** that is the bug;
- ⭐⭐ make it **prove it** with a red→green test.

Pair it with obra's [[superpowers]] [systematic-debugging skill](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md): *"ALWAYS find root cause before attempting fixes."*

## Why the words matter

Adding just **"red-green TDD"** to a prompt measurably raises quality: it hands the agent a deterministic loop it can iterate against autonomously, and the proof is a side effect. As a standing rule in [[agents-md|AGENTS.md]]: *"all code is written TDD: first the test is red, then the method, then green"* — the agent can no longer claim done without evidence. The cost is lots of code and tests, so scale it to the task's risk ([[2026-05-25-ai-government]]).

The rule applies to *your* trust as much as to the agent's: **don't trust a test you haven't seen fail**. An AI once inserted A, B, C *already sorted* into the fixture and the sorting test passed green ([[2026-08-27-ai-it-services]]). To check that an AI-written test really tests something, surgically delete the feature from production code and watch it go red — automated, that is [[mutation-testing]].

## Recipes

- **Bug from the UI**: two screenshots + *"reproduce it yourself in the browser, write a Playwright test that's red, then fix until green."* The next level is **TDD from the UI**: reproduce in a browser → failing e2e test → fix ([[2026-07-23-ai-software-product]], [[browser-mcp]]). The same chain can span two agents — one reproduces and writes the failing test, a fix agent turns it green ([[agentic-corporation]]).
- **Feature, outside-in (ATDD)**: write 3–5 KISS Gherkin acceptance scenarios *before* code, review them with the PO, co-write them with the tester after refinement, and let the agent implement against the **failing features** ([[acceptance-test-bdd]], [[2026-09-28-ai-gaming-tech]]). Read the glue code too, not only the Gherkin. Business finds them mundane — use them where the branching is heavy.
- **Two hands on the agent**: the spec markdown, where you anticipate the dramas, plus feature files or tests written upfront — the tighter you squeeze, the lighter the review ([[spec-driven-development]], [[2026-08-31-ai-software-vendor]]). The unit tests the agent writes for itself mostly save it tokens; their value as a guard is "shady".

## Keep the inner loop fast

A TDD-ing agent should run **only the fast, relevant unit tests**; anything slower than ≈5 s belongs outside the inner loop — in a harness stop hook, a git pre-push hook, or CI ([[2026-07-22-ai-agentic-how]], [[agent-lifecycle-hooks]], [[ci-green-loop]]). But the agent must not say "done" until it has run every test it can — otherwise *you* become its waiter (*"Master, let me run the tests for you"*, see [[chop-ai-waiter]]). Without a runnable test loop — no shell — agentic work degrades into a fancier chat ([[2026-05-25-ai-government]], [[2026-06-08-ai-security-tools]]).

## Limits

Green is only as good as the test. An agent under pressure will make a test pass the wrong way — weakening an assertion, skipping, deleting — so guard the tests themselves ([[reward-hacking]]), keep acceptance tests out of the coder's reach where the stakes justify it, and review the tests rather than the implementation ([[code-review]]).

## See also
- [[feedback-loop]]
- [[acceptance-test-bdd]]
- [[mutation-testing]]
- [[superpowers]]
- [[hallucination]]
- [[sycophancy]]
- [[chop-ai-waiter]]
- [[reward-hacking]]
- [[legacy-migration-stages]]
