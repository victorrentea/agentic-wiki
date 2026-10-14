---
title: Vibe Coding
category: concept
tags: [agentic-engineering, anti-pattern, context-anxiety, product-owner, human-factors, cognitive-debt]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-12-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-11
updated: 2026-10-13
---

<span style="color:red">Vibe coding is shipping a technical solution you don't understand — not merely code you didn't type, or even didn't read — the opposite end of the spectrum from disciplined [[agentic-engineering]].</span>

## What it means — the definition moved

<span style="color:red">Karpathy coined the term in Feb 2025 as generating software you never read: [*"forget that the code even exists"*](https://x.com/karpathy/status/1886192184808149383). This page used to define it that way. Through 2026 the sessions sharpened it in two steps:</span>
1. <span style="color:red">**It's not about *writing* code, it's about *reading* it.** AI writing 100% of your code is not vibe coding: reviewed, tested and explainable code is "software development" ([Simon Willison](https://simonwillison.net/2025/Mar/19/vibe-coding/); [[2026-07-20-ai-telecom]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-09-04-ai-software-company]]).</span>
2. <span style="color:red">**By autumn 2026, it's about *understanding*.** Vibe coding = **you committed a technical solution whose trade-offs you didn't understand** ([[2026-09-02-ai-insurer]], [[2026-09-16-ai-safety-critical]], [[2026-09-18-ai-agentic-how]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]). Not reading every line may stop being a mortal sin; saying "LGTM" to whatever Claude says means you no longer own the solution.</span>

<span style="color:red">Two lines worth repeating in a room: *"Not reading code is an act of faith"* ([[2026-07-22-ai-agentic-how]]) and *"AI slop — when you review AI code and say LGTM without reading"* ([[2026-04-07-ai-product-team]]). And a vocabulary for honesty: say **"I vibed that"** when you never read it and **"I built that"** when you reviewed it — flip them and nobody knows what was actually checked. *"The agent can write the code, it can never own it"* ([[2026-08-12-ai-bank]]).</span>

<span style="color:red">Milestones ([[2026-09-16-ai-safety-critical]]): the term (Feb 2025) → [Opus 4.5](https://www.anthropic.com/news/claude-opus-4-5) (Nov 2025) as the inflection point — a *model*, not a tool: with enough context it writes better code than most developers ([[2026-08-03-ai-telecom-ops-qa]]) → by early 2026, teams reporting projects where nobody reads the code (StrongDM's [software factory](https://simonwillison.net/2026/Feb/7/software-factory/), OpenAI's [harness engineering](https://openai.com/index/harness-engineering)). Simon Willison [predicts](https://simonwillison.net/2026/Apr/2/lennys-podcast/) that by 2027 half of developers will generate more than 95% of their code ([[2026-09-24-ai-bank]]). *"It is unethical to write code by hand if the AI writes it better than you"* ([[2026-07-20-ai-telecom]]).</span>

## Where vibing is legitimate

<span style="color:red">The dividing line is **blast radius, not taste** ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]], [[2026-09-30-ai-enterprise]]). *"Vibe-code your tooling without mercy; never vibe-code software 100,000 users will run"* — Victor's own interaction tool is ≈200k vibe-coded lines with fewer than 100 ever read ([[2026-09-18-ai-agentic-how]]). Vibe without remorse where it hurts no users, money or brand:</span>
- <span style="color:red">**Developer tooling never deployed to prod** and internal tools for ≈10–80 users — best on a personal subscription.</span>
- <span style="color:red">**Throw-away one-shot scripts**, e.g. a data migration.</span>
- <span style="color:red">**High-level design or plan exploration** — no code produced at all.</span>
- <span style="color:red">**Business-idea prototypes**: vibe 2–3 alternative implementations, pick the best, then rebuild progressively or [[productionalize]] it by reviewing 100% of the code ([[2026-04-27-ai-healthcare-software]]). Boris Cherny reportedly vibed ≈20 versions of a feature in one session and shipped the most stable: when experimentation costs ≈0, production-grade quality is the only metric ([[2026-05-06-ai-ecommerce]]).</span>
- <span style="color:red">☢️ **POCs** — "incarcerated" in guardrails, because business may say *"it works, ship it"* ([[2026-08-31-ai-software-vendor]], [[2026-09-04-ai-software-company]]).</span>

<span style="color:red">**Never** for code a bank maintains for 30 years, or for production code teammates depend on ([[2026-08-27-ai-it-services]]). Victor's bar for skipping review: ≈100 consecutive reviews with zero findings — or, softer, after review #50 with no problem found, *maybe* start skipping ≈30% of the simple ones, slowly. The pledge he makes rooms repeat: **"I shall not push code without reviewing it."** *"If you push it, you own it"* ([[2026-04-07-ai-product-team]]). *"We are responsible for the code we ship."*</span>

<span style="color:red">The "**SaaS apocalypse**" — replacing subscriptions with home-built tools — works only for low-risk internal or visual utilities. Bought core software (an issue tracker, a wiki) embeds 15 years of patched corner cases you would inherit, plus its bugs and upgrades; and business habits don't move to a new tool just because a developer can build one ([[2026-09-02-ai-insurer]]).</span>

## What to own even when vibing

<span style="color:red">**Skip the semicolons, own the moving parts** ([[2026-09-24-ai-bank]]): what connects to what and in what order, the concurrency protections, the micro-architecture. Review the coarse-grained tests — e2e, ideally business-confirmable Gherkin ([[acceptance-test-bdd]]) — with Playwright screenshots of every step. When a strong model fails at medium/high effort, it usually lacked context or tools — your fault, not the model's ([[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">*"It's just another abstraction level, like a compiler"*? No — **a compiler is deterministic and proven; an LLM isn't** ([[2026-09-30-ai-enterprise]]).</span>

## How long can you vibe?

Vibing works for a while, then rots. One test: ≈**3 months** of solo mania-pushing before it collapsed and engineering had to come back — diagrams, tests, decoupling. <span style="color:red">Victor's own project hit the same **≈2–3-month horizon** after ≈$3,000/month of tokens: race bugs, deadlocks, performance issues, a security glitch, [[drift]] (wrong API calls at scale) and [[vibe-heresy]] — dead ideas resurrecting from leftover `.md` files ([[2026-07-22-ai-agentic-how]]). He had vibed it deliberately *"to see the shit accumulate — and it accumulated"*; a vibe-coded search did `findAll()` + in-memory filter — *"Denial of service. Boom! Out of memory"* ([[2026-04-07-ai-product-team]]).</span>

A machine signal of the same rot is **"context anxiety"**: <span style="color:red">past some size the model becomes reluctant to read more files, stops checking for duplication, and stops writing tests — one session put it at ≈600k tokens, another near ≈300k, where Opus "knows it will enter the dumb zone" and dispatches minions instead ([[2026-07-01-ai-java-community]]). Both numbers are anecdotal; no published measurement backs a threshold</span> — a direct symptom of the [[dumb-zone]] (see [[context-window]]).

The human cost of that collapse has a name: [[cognitive-debt]] — within 2–3 months nobody knows what's in the codebase and every bug fix spawns new ones. The trap that accelerates it is fake test coverage: an unreviewed 85%-coverage suite the AI rewrites alongside the code asserts only what already happens. The durable antidote is **business-reviewed `.feature` files** ([[acceptance-test-bdd]]), not more unit tests.

## Vibe addiction

<span style="color:red">**Vibe addiction** is epistemic dopamine without self-control — one developer spent ≈€1,000/month and was nearly fired. **"Sleep vibing"**: tired or euphoric, you slide into "go, go" without really approving anything ([[approval-fatigue]]). What you built in an evening is **scaffolding, not a product**; the remedy is "code field trips" until you understand the technical solution ([[2026-09-30-ai-enterprise]], [[ai-addiction]]).</span>

## The role shift

The durable trajectory is not "no engineering" but a **role shift**: developers become product owners / "managers of agents" who steer the big moves. Business may "vibe" a prototype for 2–3 days, then hand the rotted ship to engineers for de-shitification (a real, if crude, emerging job description). The antidote to vibe-coding's downsides is the whole rest of this wiki: [[spec-driven-development]], [[acceptance-test-bdd|acceptance tests]], [[multi-model-review]], and guarding against [[skill-erosion]].

<span style="color:red">**Don't laugh at vibe-coders.** Non-technical people can out-vibe developers: they own the user need and love the product, while developers polish architecture nobody asked for ([[2026-05-06-ai-ecommerce]], [[2026-07-20-ai-telecom]]). What they can't do is tell when it's wrong. The Excel-1995 parallel: accountants feared spreadsheets, the profession grew — along with thousands of brittle business-critical spreadsheets.</span>

<span style="color:red">**"Wireframes are gone. Build it."** For UX decisions, vibe a throwaway UI — or photograph a napkin sketch and let the agent build the HTML; a designer can bring Anthropic's [frontend-design skill](https://github.com/anthropics/skills/tree/main/skills/frontend-design) — and show it in the business call: feedback today, not after two sprints of wireframes ([[2026-07-03-ai-bank]], [[2026-08-31-ai-software-vendor]]). Build-and-criticize works for UX; backend concerns (caching, paging strategy) still must be thought through, not vibed. Simulated-user sub-agents (≈10 personas clicking through the UI) help, but you still need real designers.</span>

<span style="color:red">**When business vibes the code.** After ≈10 [[grill-me|grilling]] sessions, business starts its own Claude: first impeccable tickets, then 10k–100k-line vibe-coded PRs (*"do we have a new colleague?"*) ([[2026-09-02-ai-insurer]], [[2026-08-27-ai-it-services]]). The sessions disagreed on the response. One: it's a prototype, *take away its git rights* ([[2026-08-31-ai-software-vendor]]). The newer view: denying git access only delays it — teams doing this for months call it the best thing ever, because the business finally knows what it wants. The sane contract: treat the PR as a **UX prototype — reject it as code, rebuild after grilling** ([[2026-09-04-ai-software-company]]). Business keeps the visible part (GUI/UX); you keep the technical decisions — where the cache goes and when it expires, Kafka message idempotency, observability, resilience, blue-green — and you block a narrow-minded business from shipping its vibe to prod.</span>

**The [[vibe-fixer]] role** is the economic response to vibe coding at scale: an engineer receives ≈10,000 lines of almost-working output and applies judgement — what to keep, reuse, or discard — rather than rewriting from scratch. In the [[cloud-review-workflow]], business vibes a UI draft and the developer becomes the vibe-fixer. The leverage shifts from writing to judgement. <span style="color:red">"Vibe Code Fixer" is now a LinkedIn job title; diff-visualization tools like [[code-city]] show which parts of the PR grew in complexity and coupling.</span>

*"Productionalize the vibe-coded crap — that is the profession of the future."* See [[productionalize]] for the full role description. <span style="color:red">To re-skill *into* that role, take an unfinished passion project and go full vibe for 2–3 months on a ≈€90/month subscription, in a language you can't hand-edit ([[2026-07-20-ai-telecom]], [[ai-calisthenics]]).</span>

## Shipping unread code: the dark factory

<span style="color:red">Shipping code nobody reads is possible, but needs a **massive harness first** — and infinite quota ([[2026-09-28-ai-gaming-tech]]). StrongDM's charter: *"Code must not be written by humans"* and *"Code must not be reviewed by humans"*, plus *"if you haven't spent at least $1,000 on tokens today per human engineer, your software factory has room for improvement"* ([factory.strongdm.ai](https://factory.strongdm.ai), Feb 2026; [Willison's write-up](https://simonwillison.net/2026/Feb/7/software-factory/)). "Dark factory" is Dan Shapiro's name for the top of his [five levels](https://www.danshapiro.com/blog/2026/01/the-five-levels-from-spicy-autocomplete-to-the-software-factory/) — a pattern, not a company. The anti-cheat: coding agents never see the tests, only *"requirement X failed"* — a **hidden holdout set of acceptance tests** the coder can't game ([[reward-hacking]], [[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">Uncle Bob [says he no longer reads AI-generated code](https://startupfortune.com/uncle-bob-martin-says-he-no-longer-reads-ai-generated-code-and-the-developer-world-is-split/) and watches [CRAP metrics](https://www.artima.com/weblogs/viewpost.jsp?thread=210575) instead. Victor still reviews: Uncle Bob's are sandbox projects, not legacy monsters. See [[gray-factory]] and [[agentic-corporation]] for the organizational side.</span>

## See also
- [[agentic-engineering]]
- [[dumb-zone]]
- [[spec-driven-development]]
- [[skill-erosion]]
- [[wispr-flow]]
- [[vibe-fixer]]
- [[cloud-review-workflow]]
- [[cognitive-debt]]
- [[acceptance-test-bdd]]
- [[productionalize]]
- <span style="color:red">[[vibe-heresy]]</span>
- <span style="color:red">[[ai-addiction]]</span>
- <span style="color:red">[[gray-factory]]</span>
- <span style="color:red">[[agentic-corporation]]</span>
- <span style="color:red">[[code-city]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
