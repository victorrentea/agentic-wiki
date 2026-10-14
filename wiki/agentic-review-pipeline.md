---
title: Agentic Review Pipeline
category: pattern
tags: [code-review, sub-agents, pipeline, proof-of-life, sonar, multi-model-review]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-06-25
updated: 2026-10-13
---

A structured sequence of automated and human gates that every AI-generated diff must pass before a human reviewer sees it.

## Overview

The pipeline runs before the human is ever summoned:

1. **Coder agent** writes the code.
2. **Fresh [[multi-model-review|"mother-in-law" reviewers]]** run in parallel on *different* models — never resumed sessions, always fresh context (reuse → Sonnet, simplification → GPT, efficiency → Haiku).
3. **[[static-analysis-gauntlet|Sonar]]** runs at maximum aggression.
4. **Merge attempt first** — pull dev into the branch, run all tests post-merge, before calling the human.
5. **Only then call the human ("the king")** — they review a pre-vetted diff, not raw output.
6. **Sorted review** — the human reads first where agents *disagreed* ("where there was blood"). Human attention degrades sharply after ≈30 min on a 1,500-line diff.
7. **[[policeman-agent|Proof-of-life gate]]** — nothing merges without a human sign-off token.
8. **Verdict persisted** — the review verdict is committed as a `.md` file in the PR, not left in volatile chat.
9. **Upgrade, don't hand-fix** — when the human finds a flaw, upgrade the [[sub-agents|agent's prompt]] and re-run ("upgrade the turret"), rather than patching the output by hand.

## <span style="color:red">The converged pipeline (May–June 2026)</span>

<span style="color:red">Three top companies seen in May 2026 had converged on a fuller version, with these extra steps [[2026-05-27-ai-open-workshop]] [[2026-06-02-ai-open-workshop]]:</span>

- <span style="color:red">a **mobbed brainstorm** with the BA under live transcription, and EventStorming on Miro pulled in via a Miro MCP;</span>
- <span style="color:red">an `architecture.md` drafted by AI from Jira and code, reviewed by a **second, fresh conversation** (never the author) before a human is tagged; then a `plan.md`;</span>
- <span style="color:red">**8 reviewers = 4 personas (clean code, performance, security, acceptance criteria) × 2 model families**, up to 7 loops;</span>
- <span style="color:red">on CI failure the coder **resumes its own session** (`claude -p --resume <id>`) so the reviewers' remarks are still in its history;</span>
- <span style="color:red">each PR ships with a **pre-deployed clickable environment**, reviewed from a phone on the train;</span>
- <span style="color:red">nightly merge into main with a full test rerun, and fan-out into **N cross-repo PRs** for one ticket ([[cross-repo-knowledge]]). Feature files use the team's ubiquitous language and **avoid synonyms** ("they kill agents").</span>

<span style="color:red">*"Teams running this for 3 months can't find anything real to comment on — just taste."*</span>

<span style="color:red">The loop the agent runs alone before a human sees code: git hooks → quality gate (Sonar, Fortify) → green build → `merge from main` → conflicts → start over. Knowing this "jihad" is coming, a capable agent spawns sub-agents from the start to keep its own context clean [[2026-09-07-ai-bank]] (see [[ci-green-loop]]).</span>

## <span style="color:red">Gate ordering</span>

<span style="color:red">Sources disagreed: one said run the **LLM review first**, while still in the smart zone, because it beats Sonar, leaving slow static checks to CI [[2026-09-28-ai-gaming-tech]]; a later one put **cheaper deterministic gates before any reviewer agent** — static analysis (Sonar, Snyk, Fortify, CodeQL), git hooks and linters ("no IDE when you code from your phone"), and a harness `Stop` hook that vetoes a premature "done" [[2026-10-06-gray-factory-devoxx]]. The reconciled order: **fast deterministic checks (hooks, linters, Stop-hook tests) → LLM review → slow server-side scanners in CI.** See [[enforcement-ladder]] and [[determinism-over-llm]].</span>

## <span style="color:red">Roles and orchestration</span>

- <span style="color:red">**The coder is an orchestrator, not an agent.** It must keep a clear mind while absorbing tons of review, and it is the weakest point of the whole flow [[2026-06-05-ai-consultancy]].</span>
- <span style="color:red">**Narrow single-purpose reviewers find more.** Besides clean code / performance / security / acceptance / UX / legal, add one whose only purpose in life is *"did you use the standard components or reinvent them?"* [[2026-06-05-ai-consultancy]].</span>
- <span style="color:red">**Orchestration over choreography**: an orchestrator with the big picture that delegates and collects reports beats an agent→agent chain where drift propagates. Anti-loop rule: *"you may ping-pong at most 5 times, then tag me."* [[2026-06-05-ai-consultancy]]</span>
- <span style="color:red">**Don't copy-paste between agents**: tell Opus *"send this plan for review to Codex CLI"* — they pass it themselves [[2026-06-05-ai-consultancy]].</span>

## <span style="color:red">Cost: start small</span>

<span style="color:red">Fragmenting the flow into separate agents buys accuracy, but you **pay the tokens twice** (one agent writes the markdown, the next reads it); the cheapest shape is **one conversation that grows by loading skills**. Start with a single agent plus one adversarial reviewer sub-agent, and add stages only when slop actually reaches you — the full pipeline is for legacy, not greenfield. A **triage agent** can read the ticket and decide: split it, write markdowns, or just do it [[2026-09-04-ai-software-company]].</span>

<span style="color:red">Further tuning [[2026-06-05-ai-consultancy]]: adversarial reviewers on Sonnet, only architecture and the final judge on Opus ([[model-hierarchy]]); trigger the reviewer cohort only above a risk threshold — Victor admits ≈60% of his own tasks take the ticket→code shortcut.</span>

## Why it matters

Without the pipeline, AI-generated code flows directly to human review with no pre-screening. Humans become first-line QA for obvious issues, burning attention budget on noise instead of judgment calls.

The sorted review step leverages disagreement as a signal: when two independent models flag the same spot, the human's attention is most valuable there. See [[mob-spec-review]] for the analogous human-review pattern.

## <span style="color:red">The human step</span>

- <span style="color:red">**Read Claude's PR description first** — it exposes architectural mistakes before you read any code [[2026-10-06-gray-factory-devoxx]].</span>
- <span style="color:red">**Start at the tension zones** — where the coder disagreed with a reviewer or pushed through a "minor issue" — then files sorted most → least critical ("by the 8th file your brain is flat, and that's where the boss level is"). A heat map by reviewer-concern density, or a vibe-coded IDE plugin that reddens the risky zones, helps; one company now reviews only ≈⅓ of files [[2026-05-27-ai-open-workshop]] [[2026-06-05-ai-consultancy]]. Keep PRs small enough to fit the budget: [[stacked-prs]].</span>
- <span style="color:red">**Your review comment is brain surgery on agents, not a code fix**: modify the specialised reviewer's system prompt or context and restart the flow from where it drifted, until it comes out one-shot. One company's retro rule: *justify why you reviewed code manually instead of improving a prompt* [[2026-06-05-ai-consultancy]].</span>
- <span style="color:red">**Merge is human-only, with a fingerprint** — the agent's API key has no right to merge a PR. Non-negotiable [[2026-06-05-ai-consultancy]].</span>

## Pitfalls

- Resuming reviewer agents across sessions defeats independence; always start fresh. <span style="color:red">(The *coder*, by contrast, should resume its own session after CI feedback.)</span>
- Persisting verdict in chat (not a committed file) means it disappears when the chat is closed. Use [[agents-md-discipline]] to enforce the commit step.
- Skipping the post-merge test run hides integration failures until after human review.

## See also

- [[multi-model-review]]
- [[code-review]]
- [[static-analysis-gauntlet]]
- [[sub-agents]]
- [[mob-spec-review]]
- [[policeman-agent]]
- <span style="color:red">[[stacked-prs]]</span>
- <span style="color:red">[[ci-green-loop]]</span>
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[model-hierarchy]]</span>
- <span style="color:red">[[gray-factory]]</span>
