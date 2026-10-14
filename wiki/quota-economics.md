---
title: Quota Economics
category: concept
tags: [token-economy, cost-optimization, billing, subscription, copilot, estimation, budget]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Quota economics is the money side of the [[token-economy]]: how subscriptions, API keys and per-seat credits price the same tokens very differently, and how to budget, estimate and negotiate the quota an agentic engineer needs.

## Three ways to pay for the same tokens

From cheapest to most expensive per token, as repeated across sessions: **personal subscription → team/enterprise plan → raw API key** ([[2026-07-01-ai-java-community]]).

- **Subscriptions are heavily subsidised.** How much is disputed. Sessions quoted 10×, 20×, 30–40× and up to 60× from presenters' own dashboards — e.g. ≈$6,000 of API-priced usage in a month on a ≈€90–100 plan ([[2026-09-04-ai-software-company]], [[2026-09-30-ai-enterprise]]), ≈$1,800 in one week ([[2026-09-25-ai-bank-migration]]), ≈$750 on a $20 plan ([[2026-09-07-ai-bank]]) — but none of these is a published measurement. The one sourced figure is ≈**8×**: $1,588 of API-equivalent Claude Code usage in 30 days on a $200 Max 20x plan ([Paweł Huryn, Apr 2026](https://www.productcompass.pm/p/claude-code-pricing)). Say *"several to tens of times cheaper, depending on usage"* and measure your own with [`ccusage`](https://github.com/ryoppippi/ccusage).
- **Plan credits are not what they seem.** Since Oct 2026, Max 5x / 20x include $100 / $200 a month of API credits — but they apply only to API, Agent SDK and `claude -p` runs with a Console key, **not** to interactive Claude Code ([support article](https://support.claude.com/en/articles/17154008-monthly-api-credits-for-max-and-team-plans)).
- **No headcount rule.** Rooms repeatedly claimed the Pro/Max subsidy is limited to companies under ≈150 employees. Anthropic publishes no such threshold: Pro/Max are [individual plans](https://support.claude.com/en/articles/11049741-what-is-the-max-plan), and companies move to Team/Enterprise ([[2026-09-14-ai-industrial]]).
- **Personal plan ≠ company code.** Never put a personal subscription on employer work — there is no NDA cover, and someone got in trouble for it ([[2026-08-10-ai-consultancy]], [[2026-08-12-ai-bank]]; see [[data-governance]]). The accepted middle ground: do general research, learning and tool-building on your own subscription at home, and bring only the conclusions — or the tool, via git — to work ([[2026-08-06-ai-bank]], [[2026-08-31-ai-software-vendor]]).
- The billing route also changes the cache: a subscription's main thread gets a 1-hour [[prompt-caching|cache TTL]], an API key 5 minutes.

## GitHub Copilot credits

- **Usage-based since 1 June 2026.** Copilot moved from per-request to token-based billing in GitHub AI Credits, 1 credit = $0.01; output tokens cost several times input, and higher reasoning effort *"consumes more tokens, and therefore more credits"* ([GitHub blog](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/), [pricing](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing); [[2026-08-03-ai-telecom-ops-qa]]). Why: per-request pricing let developers run *"one good prompt → 3 hours of Opus for a few dollars"* ([[2026-05-13-ai-it-services]], [[2026-07-03-ai-bank]]).
- **Not subsidised** like the model vendors' own subscriptions — Microsoft resells Anthropic/OpenAI models — so compare subscription to subscription ([[2026-08-06-ai-bank]]). "Auto" model mode bills at [0.9×](https://docs.github.com/en/copilot/reference/copilot-billing/request-based-billing-legacy/model-multipliers-for-annual-plans), a 10% discount ([[2026-09-10-testing-ai-bank]]).
- **Quota maths:** 10,000 credits ≈ $100/month ≈ $5 per working day — *"a cappuccino a day"*; a $39 seat is ≈$2/day, and *"engineers need $100+"* ([[2026-09-18-ai-agentic-how]], [[2026-09-25-ai-bank-migration]], [[2026-10-09-ai-jax-london]]). Below ≈5,000 credits a month you can't really work; ≈15–25k is realistic ([[2026-08-06-ai-bank]]). Typical corporate ladders go 3,000 → 6,000 → 12,000; defaults seen range from 10,000 to 25,000 ([[2026-09-24-ai-bank]]).
- **Prepaid and use-it-or-lose-it:** what you don't burn by month-end is gone. At ≈75% consumed, request more — approvals came in 4–8 hours, and asking often raises the monthly default ([[2026-10-01-ai-bank]]).
- **Measured burns:** one agentic task (≈700 lines touched, 12 minutes) used 1,533 credits ([[2026-08-06-ai-bank]]); an estimate on one feature ≥200 credits, because the agent must read the code; applying a full [[openspec]] change 40–90 minutes and ≥700 credits ([[2026-09-07-ai-bank]]). Use the hard `/limit` plus a soft target in the prompt ("aim for 150") — the agent actually stays within it.

## Quota is a negotiation tool, not a moral ceiling

- **Hitting your quota is the goal, not a failure** — ask for more the same day ([[2026-08-12-ai-bank]]). *"Boss, add the price of a cappuccino a day"*: against a build shortened by 40 minutes the ROI is obvious ([[2026-08-03-ai-telecom-ops-qa]]). Bosses double a quota they can *see* being earned on real work ([[2026-09-18-ai-agentic-how]]).
- A small seat burns out fast: "5 parallel agents" empty it in ≈10 minutes; parallel sub-agents *"burn three quotas"*; unbudgeted parallel Opus agents can reach ≈$100/hour — set usage alerting ([[2026-04-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]]). A conference-quoted norm: ≈**$1,000/month** in tokens per developer ([[2026-10-01-ai-bank]]).
- Out of quota? Pair with a colleague who still has some — hand them your `plan.md` and let a [[ralph-loop]] run overnight on *their* quota ([[2026-07-09-ai-live-casino]], [[2026-08-06-ai-bank]]).
- **Team quotas, not per-user**: per-user quotas hide who burned what; team quotas force healthy conversations — a pie chart of who burned 80%, and a retro on *"what did you do with that much Opus?"* as feedback, not shaming ([[2026-05-18-secure-coding-payments]], [[2026-06-08-ai-security-tools]]). Beware **token-padding**: where management tracks AI usage, some developers write code by hand and then ask the model to "make it better" just to look AI-active ([[2026-05-13-ai-it-services]]).

## Estimate in hours *and* dollars

*"4 hours, but $100"* is now a valid estimate ([[2026-09-25-ai-bank-migration]], [[2026-09-30-ai-enterprise]]). Orders of magnitude seen live:
- A typical day of 2–3 agentic tasks ≈ **$20–30** ([[2026-09-25-ai-bank-migration]]).
- Plan + implementation + review to PR ≈ **$60**, plus ≈$20 for reviewing the other agent's work; one review + `/simplify` round ≈ $22; a spec-driven paginated grid ≈ $20 in 25 minutes AFK ([[2026-08-27-ai-it-services]]).
- `/openspec-apply-change` on a day-sized feature ≈ **$15 and 20–45 minutes** ([[2026-10-09-ai-jax-london]]); a full OpenSpec apply ≈€10 for what used to be a week's work — possible only because every point was clarified in advance ([[2026-08-06-ai-bank]]).
- *"6 hours, $50"* for a grid that a team had estimated at 7–8 days by hand ([[2026-09-14-ai-industrial]]).
- **Over $50 per task is a red flag** ([[2026-10-09-ai-jax-london]]); a two-digit *session* cost is the signal to stop and ask what happened ([[2026-08-10-ai-consultancy]]).

Estimating reliably needs **historical data**: map tokens → money (input, output, cache read, cache write) on past sessions with a tool like [AgentsView](https://github.com/kenn-io/agentsview), and track **tokens per outcome** — what did this bug fix, test or investigation cost? — as a technical KPI ([[2026-08-31-ai-software-vendor]], [[2026-06-08-ai-security-tools]]). The human side has a budget too: at most ≈4 hours of hard brain per day ([[2026-10-01-ai-bank]]; see [[system-2-fatigue]]).

## Spending a personal plan well

- **Weekly budget, not monthly**: burn it in 2 days and you wait 3, not 15 ([[2026-09-14-ai-industrial]]). Time the weekly reset for **Sunday evening**, not Friday — a Friday reset means a weekend at the keyboard ([[2026-07-09-ai-live-casino]], [[2026-09-04-ai-software-company]]).
- **Friday with quota left:** point the top model at the most painful legacy area — code without tests, missing acceptance tests, flaky tests, lazy pipelines, unfinished migrations ([[2026-09-02-ai-insurer]], [[2026-09-30-ai-enterprise]]).
- **Near an empty tank:** spend the last dollars on the *architecture conversation*, not on code. One top-model run ate ≈$70 of a weekly limit ([[2026-09-04-ai-software-company]]).
- Cheaper personal plans exist for open-weight models (e.g. [OpenCode Go](https://opencode.ai/go) at $10/month, per [[2026-09-04-ai-software-company]]); see also [[claude-code-router]].

## The long view

- **Inference keeps getting cheaper**: ≈10× per year by a16z's ["LLMflation"](https://a16z.com/llmflation-llm-inference-cost/) estimate, the conservative end — [Epoch AI](https://epoch.ai/data-insights/llm-inference-price-trends) measures 9×–900× per year for equal benchmark score ([[2026-09-07-ai-bank]], [[2026-08-31-ai-software-vendor]]). AI coding won't become a corporate luxury; what is expensive today costs a fraction next year.
- **No 10× developer**: measured gains are ≈1.3× — ≈26% more completed tasks in [Microsoft Research's field experiments](https://www.microsoft.com/en-us/research/publication/the-effects-of-generative-ai-on-high-skilled-work-evidence-from-three-field-experiments-with-software-developers/) — *"a 10× learner, then a 1.3× doer"*; learn on your own money, since token budgets burn out in months, not years ([[2026-09-10-testing-ai-bank]], [[2026-09-14-ai-industrial]]).
- **Burn to change your mind**: *"until you've burnt ≈$5,000 you won't change your attitude"* ([[2026-09-30-ai-enterprise]]) — the spending-as-adoption argument in [[token-economy]].

## See also
- [[token-economy]]
- [[prompt-caching]]
- [[model-hierarchy]]
- [[data-governance]]
- [[ralph-loop]]
- [[openspec]]
- [[ai-adoption]]
- [[claude-code-router]]
- [[system-2-fatigue]]
