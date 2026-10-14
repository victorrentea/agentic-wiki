---
title: Multi-Model Review
category: pattern
tags: [code-review, sub-agents, attention-dilution, quorum, gpt, cloud-review]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-11
updated: 2026-10-13
---

Multi-model review is a code-review pattern — the "quorum of mothers-in-law" — that runs one [[sub-agents|sub-agent]] per review concern across two model families, so each concern gets full attention and no single model's blind spots dominate.

## Why a second agent: the dice math

<span style="color:red">The model is a slot machine: the same prompt yields different answers. If one agent emits slop with probability *p*, an **independent** second review lets the same slop escape with probability ≈*p*² — 1% becomes ≈0.01%, 10⁻³ becomes ≈10⁻⁶. *"You roll the dice twice"*; *"two independent LLMs missing the same slop is a probability argument, not faith"* ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-31-ai-software-vendor]], [[2026-07-22-ai-agentic-how]]). The catch is the word *independent*: **same model, same blind spots** — model errors [correlate](https://arxiv.org/abs/2506.07962), and two conversations on the same model "throw the die the same way" ([[2026-09-28-ai-gaming-tech]], [[2026-08-06-ai-bank]]). Hence the rule: **no human looks at agent-written code before another agent has reviewed it** ([[2026-09-07-ai-bank]]).</span>

<span style="color:red">Three conditions make the second roll independent:</span>
- <span style="color:red">**A fresh conversation.** Never review in the context that produced the code: the author is *"prisoner of its own mental model"*, anchored by its 400k-token story and **sunk-cost invested** — it defends its abstract factory where three lines would do. In one case a fresh reviewer found a 3× simpler way ([[2026-06-02-ai-open-workshop]], [[2026-08-27-ai-it-services]], [[2026-09-28-ai-gaming-tech]], [[2026-10-06-gray-factory-devoxx]]). Never bolt a security review onto a dying 600k-token coding conversation — *"you want a brilliant baby pen tester"*, not an exhausted coder ([[dumb-zone]]).</span>
- <span style="color:red">**Another model family.** *"GPT can smell Claude, and vice versa"*; GPT reviewing Opus is "blood" — they "hate each other" ([[2026-08-10-ai-consultancy]]). Ideally the reviewer doesn't even know who wrote the code, so it isn't anchored by the author.</span>
- <span style="color:red">**A reviewer at least as strong as the author.** Review a top-tier model's PR with an equal or stronger model, never a weaker one — *"spawn an adversarial review subagent with a superior model to criticize your work"* ("quality triples") ([[2026-09-07-ai-bank]], [[2026-09-25-ai-bank-migration]]). Opus reviewing Sonnet's spec work catches real design decisions Sonnet skipped, e.g. "never join-fetch a paginated query" ([[2026-08-12-ai-bank]]).</span>

## The full agentic review pipeline

Before the human sees code, a full pipeline runs in order — see [[agentic-review-pipeline]]:
1. Coder agent writes code.
2. Fresh "mother-in-law" reviewers on **different model families** in parallel (reuse → Sonnet, simplification → GPT, efficiency → Haiku).
3. Static analysis (Sonar) at maximum aggression.
4. Attempt the merge: pull dev into the branch, run all tests post-merge.
5. Only then call the human ("the king").

<span style="color:red">Run the cheap deterministic part **before paying another LLM**: Sonar + all tests green + mergeable PR, in the cloud rather than on your laptop — a post-push hook watches CI and fixes in a loop; before calling you, the agent merges dev and resolves conflicts itself ([[2026-07-20-ai-telecom]], [[2026-08-06-ai-bank]], [[ci-green-loop]]). After a 40-minute Sonar build, **don't resuscitate the tired coder** — spawn a fresh agent for the fix ([[2026-09-18-ai-agentic-how]]).</span>

Persist the review verdict as a **committed `.md`** in the PR — not volatile chat — so it's sortable next to the code and survives conversation resets.

## Attention dilution

A single prompt asking for clean code **and** repetition **and** performance **and** pen-testing **and** legal **and** ethics **and** UX *dilutes* the model — it attends to none of them well. The fix is to **split each concern into its own sub-agent**, and to spread them across model families (e.g. Opus plus GPT via the codex CLI) so their weaknesses don't overlap. Each reviewer is told which commits to look at and to **report only — don't act**, running in parallel. For "extra blood," drive **Codex CLI** from within Claude to review Opus's diff — cross-model adversarial review where the *reviewer model* is invoked via the CLI of a competitor harness. The result is a review without any in-group bias from the same model family. <span style="color:red">One engineer at a large software company went further: he extracted the review prompts from the Claude Code binary and fed them to Codex — cross-model review with the best-tuned prompts ([[2026-07-09-ai-live-casino]]).</span>

The full state-of-the-art workflow (seen in three companies): the coding agent implements via sub-agents, then hands off to a dedicated **review orchestrator** that spawns **≈3 sub-agents per concern** — clean code, security, performance — and runs each concern across **two model families (Opus + GPT)** ⇒ ≈6 reviewers combing the same diff.

A simpler aggregation variant runs **Sonnet + GPT in parallel** over existing PRs and **feeds both result-sets to Opus to aggregate** — the architect-tier model from the [[model-hierarchy]] reconciles the two reviews into one. A field tip with a kernel of truth: telling GPT *"the code was written by Claude"* makes its review ≈20% more aggressive — model families have a mild in-group bias, which is exactly why mixing families is worth the cost.

## Specialist reviewers: grow the quorum on evidence

- <span style="color:red">**Start with the built-ins, not the whole forum** — a full quorum burns the budget ([[2026-07-30-ai-safety-critical]], [[2026-10-01-ai-bank]]). Add a reviewer persona **only when that kind of garbage keeps reaching you**; personas that earned a slot in the sessions: simplification · reuse · performance · security · UX consistency · auditability/legal · conformance to the acceptance tests ([[2026-07-23-ai-software-product]], [[2026-07-20-ai-telecom]]). Each specialist roughly multiplies the cost.</span>
- <span style="color:red">**One maniacal agenda per reviewer.** Concrete agents live in `.claude/agents/`, e.g. `reviewer-efficiency` — a "performance maniac" hunting only N+1 queries and perf, blind to the rest ([example](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md)) — and `reviewer-simplification`, dispatched adversarially on the same diff, with the model pinned in frontmatter. The question that births each one: *"what kind of person would you want reviewing this file?"* ([[2026-08-31-ai-software-vendor]], [[2026-09-25-ai-bank-migration]], [[custom-agent]]).</span>
- <span style="color:red">**A UX reviewer** hunts inline styles and `!important` in the diff and asks *"why did you put that there?"*; pair it with pixel-perfect before/after screenshots where everything is identical except the new field — it would have caught a "pets" column collapsing onto one line ([[2026-06-08-ai-security-tools]], [[review-by-images]]).</span>
- <span style="color:red">In production, one team runs separate CI bots for performance, security, accessibility, i18n and design consistency, each commenting under its own handle (`@perf-bot`, `@sec-bot`) ([[2026-05-06-ai-ecommerce]]).</span>
- <span style="color:red">**Polish the reviewer, not the output.** Each reviewer is a prompt improved month after month: when you see garbage, fix *that reviewer's* prompt ([[2026-06-02-ai-open-workshop]], [[self-improving-knowledge]]).</span>

## Built-in review commands

<span style="color:red">Per the [Claude Code docs](https://code.claude.com/docs/en/code-review), checked 2026-10-13 — several sessions described these differently:</span>
- <span style="color:red">**`/code-review`** (alias `/review`) reviews your branch's diff locally as a background sub-agent. It reports **correctness bugs**, and, depending on model and effort level, reuse, simplification and efficiency cleanups; `--fix` applies findings, `--comment` posts them on the PR. The published [code-review plugin](https://github.com/anthropics/claude-code/tree/main/plugins/code-review) runs four parallel reviewers with confidence scoring ([[2026-09-18-ai-agentic-how]]).</span>
- <span style="color:red">**`/simplify`** is a separate, cleanup-only review that applies fixes and does **not** hunt bugs ([[2026-07-23-ai-software-product]]). The sessions' counts of its parallel sub-agents (three in one, four — reuse, YAGNI, efficiency, architecture — in another) are not in the docs.</span>
- <span style="color:red">**`/security-review`** ([repo](https://github.com/anthropics/claude-code-security-review)) scans the diff for vulnerabilities, running locally like any skill ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**Managed Code Review** (GitHub app, Team/Enterprise): a fleet of agents in parallel, a verification step to filter false positives, findings tagged by severity; **≈$15–25 per review** on average. It reads `CLAUDE.md` and a **`REVIEW.md`** at the repo root — but the local `/code-review` does **not** read `REVIEW.md`.</span>
- <span style="color:red">Put **review-only and aesthetic rules in `REVIEW.md`, not AGENTS.md** — the author shouldn't spend effort on form, and a long AGENTS.md dilutes ([[2026-10-01-ai-bank]], [[2026-10-13-ai-bank]]). Copilot's review honours `REVIEW.md` too ([[2026-09-18-ai-agentic-how]]); Codex reads a review section of AGENTS.md ([best practices](https://developers.openai.com/codex/learn/best-practices)).</span>

## Report only, one round, the author fixes

- <span style="color:red">**Report-only is non-negotiable.** Never let a reviewer write or run tools. Appending *"…and fix any findings"* to a reviewer prompt makes the parent agent **blindly obey** the list ([[2026-09-25-ai-bank-migration]], [[2026-07-23-ai-software-product]]).</span>
- <span style="color:red">**The coder must not decide whether to call its reviewers.** Wrap it in a hard-coded workflow, a Stop hook or CI that calls them — *"the coder is just a worker in a cubicle"* ([[2026-07-22-ai-agentic-how]], [[agent-lifecycle-hooks]]). Reviewers get **the same inputs as the coder** — ticket, acceptance criteria, the human-approved spec `.md` files — **plus the git diff** ([[2026-07-30-ai-safety-critical]]).</span>
- <span style="color:red">**The author fixes**, not a third agent that would rediscover all the code. It may fix, escalate to a human, or **reject a finding as a false positive** — a reviewer told to find nits *will* find nits, and a [sycophantic](https://www.anthropic.com/research/towards-understanding-sycophancy-in-language-models) reviewer invents issues to please you ([[2026-09-07-ai-bank]], [[2026-10-01-ai-bank]], [[sycophancy]]). Spawning the reviewer *from* the implementer has a benefit: the parent trusts its child's findings and still holds the session context ([[2026-09-28-ai-gaming-tech]]).</span>
- <span style="color:red">**One round, not a loop.** Earlier advice was to have reviewers loop 3–7× until they go quiet ([[2026-06-02-ai-open-workshop]]); by autumn 2026 the consensus is **a fresh reviewer per review, but a single round**: asked for more issues after the fixes, it invents false ones; *"look again"* always finds something ([[2026-08-27-ai-it-services]], [[2026-09-07-ai-bank]], [[2026-09-28-ai-gaming-tech]]). If the adversarial reviewer needs iterations, the task breakdown was too big ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**When the findings are bad, upgrade the rule, then rewind**: fix the CLAUDE.md, skill or reviewer agent, discard the review, and rerun the critic **once** from the original code in a fresh context.</span>

## What it costs

<span style="color:red">Review on a strong model is cheap because **a reviewer reads a lot and writes little**, and output tokens cost 5× input ([[2026-07-23-ai-software-product]], [[2026-09-07-ai-bank]], [[2026-09-24-ai-bank]]). Data points from the sessions: ≈200–300 AI credits per PR, about a **third of the implementation cost**; one feature at **$23 implement, $6 review, $6 fixes**; a pagination review at $7 — *"a lunch"*; four adversarial reviewers ≈€20 on a 10,000-line PR ([[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]], [[2026-09-18-ai-agentic-how]]). A multi-agent `/code-review` multiplies tokens versus one reviewer asked verbally ([[2026-09-14-ai-industrial]]). Accept the doubled reading cost: it's the price of the independent roll. The cheapest way to convert sceptics: Opus at high effort that only leaves PR comments and changes nothing ([[ai-adoption]]).</span>

## Review the markdown and the plan, not just the code

- <span style="color:red">**Review the plan first** for open-ended, high-stakes work — a wildly different approach is too late to catch at code-review time. On **provable** work (test green, latency low) let the loop run; on work without a fast feedback signal (architecture, splitting a service, caching, a race that surfaces in months) you are the evaluator: **1 agent writes, 2 review (Opus + GPT), then a human** — a second run critiquing the first, not N parallel proposals that grind your brain ([[2026-06-02-ai-open-workshop]], [[2026-07-23-ai-software-product]], [[advisor-vs-executor]]).</span>
- <span style="color:red">**Review the markdowns in a fresh terminal**: *"review the markdowns in this change and tell me if you find any glitch"* — a new Opus instance immediately flagged a requirements/design contradiction; a decoupled sub-agent or Codex can read proposal + design + tasks and report inconsistencies ([[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]], [[openspec]]). Have the agent render a **visual** of the plan (Kanban, sequence diagram) — far easier to review than 300 lines of markdown.</span>
- <span style="color:red">**2× review per markdown phase, fewer where there is an oracle.** The brainstorm → 2× plan review → design → 2× design review → implement → 2× code review cadence of [[superpowers]] pays off for markdown, which has no executable check; when tests, lint and type-check exist, the oracle can replace a review round. And ≈80% of tickets ("add one more field everywhere") don't deserve the spec → design → plan ceremony at all ([[2026-08-06-ai-bank]]).</span>
- <span style="color:red">**During coding**, the same trick is an *advisor*: a second mind from another family, without the bias of decisions already taken ([[2026-07-30-ai-safety-critical]], [[advisor-vs-executor]]).</span>

## Review sorted, not top-to-bottom

Don't read a PR in file order — your reviewer brain dies roughly halfway through a big one. Have the quorum review first, then **surface the lines the reviewers disagreed on first**: spend your sharpest minutes where the bots fought, not on the boilerplate they all approved. "Review sorted, not selective" is the one trick that shrinks PR-review pain to ≈10% of the effort. See [[code-review]] for the human side (PR-size sweet spot, judgement vs mechanics).

For adversarial effectiveness, spawn **fresh** reviewer sub-agents each round rather than resuming a previous reviewer — fresh context means fresh randomness and no anchoring bias from prior rounds. And surface **coder–reviewer disagreements first** to the human reviewer: the lines the models fought over are where your sharpest attention should go, not the boilerplate they unanimously approved.

<span style="color:red">A concrete form is a **review handoff file** (`review.md`) the coder writes: its assumptions **with confidence ratings** (skip those rated above ≈90%), the findings it refused to fix, and the ones it auto-fixed, posted as PR comments ([human-review demo](https://victorrentea.github.io/human-review/demo/review.html), [[assumptions-ledger]], [[human-review-dashboard]]). The human reads in this order ([[2026-10-01-ai-bank]], [[2026-09-28-ai-gaming-tech]]): (1) findings the author **refused**; (2) **auto-fixes** — the reviewer may have invented them; (3) decisions it hesitated on. The cheapest version: at the end, ask the author *"where weren't you sure?"*. Generated review markdown is best read in pairs, bullet by bullet; when nobody understands a point, put the agent in teacher mode ([[2026-08-31-ai-software-vendor]]).</span>

## When slop gets through anyway

<span style="color:red">Slop that passed the whole chain — Sonar, linters, guardrails, LLM judge, $22 of review — is not fixed locally. Do "brain surgery" on the root markdown (a few lines of upgrade to rules, skills or guardrails; [[self-improving-knowledge]]), then **verify the upgrade** ([[2026-08-31-ai-software-vendor]]): *soft* — restart a clean reviewer without telling it what changed and see if it finds the problem; *medium* — revert the work and re-run with the new rules; *hardcore* — re-implement on a parallel branch and have an [LLM judge](https://arxiv.org/abs/2306.05685) compare the two ([[judge-llm]]). Every lint finding a human still catches demands a level-up of the layer that missed it ([[2026-10-01-ai-bank]]).</span>

## "Done" includes "I watched it work"

The coding agent shouldn't even *request* review until a [[sub-agents|sub-agent]] has **driven the running app** — deployed to a dev environment, clicked through with [Playwright](https://playwright.dev), screenshotted, written and run the [[acceptance-test-bdd|acceptance tests]] in the browser. Only *then* do the "stylist" review bots critique look-and-feel, performance, security and pen-testing. Driving the app is common sense — it isn't the review.

## What humans still review

The agents handle breadth; humans handle judgement. Review the [[acceptance-test-bdd|acceptance tests]] and the code, never the AI's mock-coupled unit tests. And **pair-read the spec**: 10 minutes each, alternate, break when a brain dies ("you notice the other guy died → stop") — the human guard against [[skill-erosion]] and fatigue-LGTM. PR review is one of the two real bottlenecks left (the other is writing the spec).

<span style="color:red">Size matters more than ever: 1,500 lines committed in an hour means human review is dead — under 200 lines you find 10 issues, over 1,000 you LGTM ([[2026-08-27-ai-it-services]]). Split into small [[stacked-prs]] (one session: *"seven small beat three big"*, under 500 lines each; another warned that stacks can fake progress when one fix ripples through them) and **park curiosities in a second agent terminal** instead of growing the PR ([[2026-10-01-ai-bank]], [[2026-10-06-gray-factory-devoxx]]). Review a colleague's PR with **your own** agent even if they already reviewed with theirs, wire it to post findings back as PR comments — but never paste raw AI comments you haven't understood ([dontpastetheai.com](https://dontpastetheai.com/)): if it didn't pass through your brain, you can't say "this is minor" ([[2026-08-12-ai-bank]], [[2026-08-06-ai-bank]]). Relax human review only after proof — e.g. full review until ≈20 consecutive clean PRs, then by risk ([[gray-factory]], [[code-review]]).</span>

**Push review knowledge to the reviewer stage, not the writer.** Keep the coding agent minimal (it stays in its "smart zone"), and load hundreds of clean-code, performance, and security rules into the reviewer sub-agents. When you spot a real flaw in a review: (1) encode it as a rule, (2) re-run it on the existing generated code immediately — "tomorrow never comes," and (3) spawn a fresh sub-agent to verify that the rule actually catches the mistake before trusting it.

**Cloud embedding:** in the [[cloud-review-workflow]], the review orchestrator is a cloud bot triggered by PR creation — no laptop needed. The "weakest link is us" framing motivates the whole pattern: code ships ≈3× faster than review scales, and sorted multi-model review is the structural response. <span style="color:red">Opus reviewers via `claude -p` in GitHub Actions can replace a free Copilot PR review ([[headless-claude]]); in the enterprise variant, the adversarial review runs on **every merge request from the DevSecOps pipeline** ([[2026-09-28-ai-gaming-tech]], [[2026-10-13-ai-bank]]).</span>

## See also
- [[sub-agents]]
- [[code-review]]
- [[static-analysis-gauntlet]]
- [[acceptance-test-bdd]]
- [[skill-erosion]]
- [[spec-driven-development]]
- [[claude-code-router]]
- [[cloud-review-workflow]]
- [[model-hierarchy]]
- [[agentic-review-pipeline]]
- [[mob-spec-review]]
- <span style="color:red">[[advisor-vs-executor]]</span>
- <span style="color:red">[[assumptions-ledger]]</span>
- <span style="color:red">[[stacked-prs]]</span>
- <span style="color:red">[[superpowers]]</span>
- <span style="color:red">[[sycophancy]]</span>
- <span style="color:red">[[judge-llm]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
