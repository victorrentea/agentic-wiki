---
title: Effort Setting
category: concept
tags: [reasoning, configuration, token-economy, model-choice, claude-code]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-11
updated: 2026-10-13
---

<span style="color:red">The effort setting is the knob that controls how much reasoning a model spends per turn; the stable practical advice is to **match effort to the phase of work and almost never use `max`**.</span>

## The levels and the defaults

<span style="color:red">Claude Code's `/effort` offers `low`, `medium`, `high`, `xhigh` and `max` on Opus 4.7 and later, Sonnet 5/5.5, Haiku 5.5 and Fable; Opus 4.6 and Sonnet 4.6 have no `xhigh` (it falls back to `high`). In spring 2026 the menu was still `low | medium | high | max` ([[2026-05-04-ai-telecom]]). The default is `high` on every model **except** Opus 5.5, Sonnet 5.5 and Haiku 5.5, which default to `medium`, and Opus 4.7, which defaults to `xhigh` ([Claude Code docs](https://code.claude.com/docs/en/model-config#adjust-effort-level)).</span>

<span style="color:red">Anthropic's starting point depends on the model ([effort docs](https://platform.claude.com/docs/en/build-with-claude/effort)):</span>

| <span style="color:red">Model</span> | <span style="color:red">Recommended start</span> |
|---|---|
| <span style="color:red">Opus 4.7 / 4.8</span> | <span style="color:red">`xhigh` for coding and agentic work</span> |
| <span style="color:red">Opus 5</span> | <span style="color:red">`high`; step up to `xhigh` for demanding coding</span> |
| <span style="color:red">Opus 5.5</span> | <span style="color:red">`medium` — per Anthropic it matches or beats Opus 5 at `high`</span> |

<span style="color:red">This is the pattern the sessions found by trial: **smarter model, lower effort** — "Opus 5 at medium ≈ Opus 4 at high" ([[2026-09-18-ai-agentic-how]]). The default drifts down as models improve.</span>

## Why not max

At **max**, the model "starts talking about the meaning of life / why we write tests" and burns enormous reasoning tokens for little gain — a [[token-economy]] sink with diminishing returns. <span style="color:red">Sessions add that it lengthens loops, can burn an hour's budget, and *sometimes reasons worse than high* — "it gets philosophical", "it goes psychopath" ([[2026-05-04-ai-telecom]], [[2026-08-27-ai-it-services]], [[2026-09-14-ai-industrial]], [[2026-09-28-ai-gaming-tech]]). The docs agree in softer words: `max` "may show diminishing returns and is prone to overthinking, so test before adopting it broadly" — discouraged, not forbidden. Claude Code applies `max` to the current session only, unless you set it through the environment variable ([docs](https://code.claude.com/docs/en/model-config#adjust-effort-level)). The one exception Victor allows: an architecture plan on Opus ([[2026-09-04-ai-software-company]]).</span>

## Effort by phase

<span style="color:red">Earlier advice on this page was "use `xhigh` for everything". Ten sessions between July and September 2026 contradicted it, and Victor himself dropped from `xhigh` to `high` on Opus 5 after `xhigh` exhausted his quota with a single terminal open ([[2026-08-03-ai-telecom-ops-qa]]). The consensus by autumn 2026:</span>

| <span style="color:red">Work</span> | <span style="color:red">Effort</span> |
|---|---|
| <span style="color:red">Writing code, debugging, the test-fail-fix loop, QA automation</span> | <span style="color:red">**medium** on Opus (or Sonnet at high)</span> |
| <span style="color:red">Planning, single-repo architecture, review, "which logic goes into which microservice"</span> | <span style="color:red">**high**</span> |
| <span style="color:red">Microservice-wide architecture, hard legacy, multi-repo, serious refactors, unknown code or a sensitive area</span> | <span style="color:red">**xhigh**</span> |
| <span style="color:red">Ops / production incidents</span> | <span style="color:red">top model, **high**, 1M context</span> |
| <span style="color:red">Anything</span> | <span style="color:red">never **max**; never **low** for Opus writing code</span> |

<span style="color:red">Sources: [[2026-07-22-ai-agentic-how]] (start `xhigh` for planning, drop to medium/low for pure code-spitting once the plan is fixed), [[2026-08-10-ai-consultancy]], [[2026-08-12-ai-bank]] (`high` is the sweet spot, `medium` on a budget, never `low`), [[2026-08-13-ai-insurer]], [[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]], [[2026-09-07-ai-bank]] ("medium or high, never max"), [[2026-09-28-ai-gaming-tech]] ("high is the ceiling for most work"), [[2026-09-30-ai-enterprise]]. The "medium for code, higher for decisions" split exists because plans and architecture are far-horizon and hard to verify, while code gets checked by tests within minutes.</span>

<span style="color:red">For legacy work and real environments the sessions set a floor: Opus-class, 1M context, effort ≥ high — "100,000 tokens is a joke in real environments" — never Sonnet at medium ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-10-ai-consultancy]]). One more rule of thumb: reasoning effort high once the context passes ≈15k tokens ([[2026-08-06-ai-bank]]).</span>

## What reasoning costs

<span style="color:red">**Thinking tokens are billed as output tokens**, which is why Opus ends up ≈3–5× Sonnet per task rather than 2×: Opus thinks ≈3× more on the same task ([[2026-05-06-ai-ecommerce]], [[2026-05-13-ai-it-services]]). Lower or disable thinking for trivial lookups ("what version is X?"). Reasoning also stays in the context: on current models (Opus 4.5+, Sonnet 4.6+, Haiku 5.5, Fable) the API **keeps every previous turn's thinking blocks** and bills them as input on later turns ([thinking docs](https://platform.claude.com/docs/en/build-with-claude/thinking#thinking-block-preservation-by-model)). Terse-output skills like [[caveman]] shrink only the visible answer, not the thinking — cut reasoning with effort instead.</span>

<span style="color:red">The thinking isn't for you, but its first lines tell you immediately whether the model understood the task ([[2026-09-14-ai-industrial]]). One session also noted that changing effort mid-session can invalidate the [[prompt-caching|prompt cache]] ([[2026-09-02-ai-insurer]]), so pick a level per conversation rather than toggling.</span>

## Effort vs model choice

Effort is one of two reasoning dials, the other being which model you run:

- **Downgrade Opus → Sonnet** for simple work to save cost and context. <span style="color:red">But **on non-trivial problems Opus at medium beats Sonnet** — the rework after Sonnet costs more than you saved ([[2026-08-27-ai-it-services]]).</span>
- **Don't use Haiku at the office** — reserve it for fast scans or a single-file rename, never for engineering. <span style="color:red">A typical job sheet: top planning model for plans; Opus or a top GPT for architecture and hard code; Sonnet high or Opus medium for coding and QA automation; Haiku-class for docs, compliance, browser clicks and logs ([[2026-09-30-ai-enterprise]], [[model-hierarchy]]).</span>

Both dials feed the same [[token-economy]] discipline and interact with the [[dumb-zone]]: more reasoning fills the [[context-window]] faster, so spend it where it earns its keep.

## See also
- [[token-economy]]
- [[dumb-zone]]
- [[context-window]]
- <span style="color:red">[[model-hierarchy]]</span>
- <span style="color:red">[[prompt-caching]]</span>
- <span style="color:red">[[plan-mode]]</span>
- <span style="color:red">[[caveman]]</span>
- [[2026-06-11-ai-mobile-gaming]]
