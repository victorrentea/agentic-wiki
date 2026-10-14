---
title: Example-Driven Prompting
category: pattern
tags: [prompting, few-shot, examples, refactoring, legacy, pattern-matching]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-04-ai-telecom]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Example-driven prompting means showing the agent concrete examples — "from this 💩 to this ⭐" — instead of describing what you want in prose, because pattern-matching is an LLM's star feature: *"AI loves examples."*

## Why it works

A model imitates what it sees far more reliably than it follows an abstract description. Prose leaves room for the training-data default; an example pins the shape, the naming, the level of detail and the edge cases at once. *"AI without examples is shooting in the dark"* ([[2026-04-27-ai-healthcare-software]]). The flip side is just as strong: the agent also imitates the *bad* examples it finds in your codebase — see [[light-dark-code]]. Anthropic's own prompting guide makes the same point: [use examples effectively](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively). In calisthenics terms ([[ai-calisthenics]]): **give an example instead of babysitting** ([[2026-09-25-ai-bank-migration]]).

## Scales of example

### In the prompt — few-shot cases

Include **matching and non-matching** cases and let the model infer the rule. For a search box: `Ra` matches `Fran`; accents ignored (`ț ≈ t`); debounce ≥0.5 s; cancel in-flight requests. That level of specificity separates a 30-minute task from a 3-day back-and-forth ([[2026-04-07-ai-product-team]]). Negative examples matter as much as positive ones: they encode the [[non-goals]]. The same trick drives a [[judge-llm|judge]]: give it examples and counter-examples of on- and off-topic requests.

### In the repo — example files

A large refactor went wrong until the second pass: *"here are two example files — follow this shape."* Example files beat any amount of description. A docs writer agent produced release notes explaining *how/what* but never *why* — until it got **one example doc** plus the tickets and the code ([[2026-04-27-ai-healthcare-software]]).

### At codebase scale — exemplar modules

Point the agent at an exemplar module to imitate ([[light-dark-code]]); for greenfield, hand-write one spike flow first so there *is* something to imitate. Before unleashing an agent on legacy: **repay tech debt and finish half-done migrations** — the agent copies every failed initiative it finds ([[2026-10-06-gray-factory-devoxx]], [[legacy-migration-stages]]).

## Concrete → abstract

The workflow that makes this repeatable:

1. **Polish one slice by hand** (or with the agent, tightly steered) — 1–3 representative files, or one screen; in one legacy case ≈100 commits on a single slice.
2. Ask the agent to **extract the pattern** into a markdown recipe or [[agent-skill|skill]] — or, for mechanical changes, into an [[openrewrite]] recipe that applies it syntax-safely.
3. **"Apply my diff everywhere"** / *"do the rest"* ([[2026-09-25-ai-bank-migration]], [[2026-10-06-gray-factory-devoxx]]).

When writing the recipe itself, iterate on **counter-examples first** — e.g. for an "inline single-use variable" recipe, the variable used twice that must *not* be inlined ([test](https://github.com/victorrentea/petclinic/blob/main/refactoring-legacy/src/test/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariableTest.java)). A hand-written OpenRewrite recipe used to take a week; Copilot wrote that one in 10 minutes.

## Examples vs. goals

Examples tell the agent *what good looks like*; they don't tell it *why*. Pair them with the goal ([[goals-over-instructions]]) so the agent can generalise to cases the examples don't cover, and with a [[grill-me|grill]] when the examples themselves leave a decision open. Without either, an agent that only has prose tends to [[ai-spiraling|spiral]].

## See also
- [[light-dark-code]]
- [[openrewrite]]
- [[agent-skill]]
- [[goals-over-instructions]]
- [[non-goals]]
- [[judge-llm]]
- [[grill-me]]
- [[spec-driven-development]]
- [[ai-calisthenics]]
