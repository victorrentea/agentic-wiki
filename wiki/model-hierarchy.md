---
title: Model Hierarchy
category: concept
tags: [models, opus, sonnet, haiku, fable, gpt, orchestration, cost, auto-routing]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-12-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-23
updated: 2026-10-13
---

The model hierarchy is the discipline of matching the model tier to the task's difficulty — read like an "experienced-hire" analogy — instead of always reaching for the strongest (expensive) or cheapest (unreliable) model.

## The experienced-hire analogy

- <span style="color:red">**Fable = the outside consultant** (new tier above Opus, autumn 2026).</span> <span style="color:red">Not for writing everything: review and architecture, cross-service design, "stinking legacy" where Opus fails the implementation, multi-hour autonomous runs — and as **chief orchestrator** delegating implementation to Opus. It has a [separate quota bar that eats your weekly limit](https://support.claude.com/en/articles/15424964-claude-fable-models-on-your-plan).</span>
- **Opus = senior architect (10–15 yrs).** Mandatory for legacy code and the best orchestrator in multi-agent flows — the one you put in charge of farming work out to cheaper models. Strongest on long horizons (see [[dumb-zone]]). <span style="color:red">Sessions stretch the analogy to a 20- or even 40-year architect; Opus 4.5 (Nov 2025) was the inflection point — "better code than a 20-year senior, given the same context." Hence the blunt rule: *"whenever Opus 4.5 or above misses, it's your fault — you didn't give it enough context"* ([PEBKAC](http://www.catb.org/jargon/html/P/PEBKAC.html)).</span>
- **Sonnet = mid-level dev (2–5 yrs).** Excellent for greenfield day-to-day work — but **don't point Sonnet at a big legacy codebase**; it lacks the architect's reach. <span style="color:red">It carries ≈80–95% of daily coding in most rooms, needs guidance, and "rushes to impress" ("what next, boss?") — so keep it off planning and review.</span>
- **Haiku = graduate hire (<1 yr).** <span style="color:red">Never for code you will maintain, never for judgement calls or autonomous review (see the [[ralph-loop]] warning about Haiku as a judge). Its real niche is **lots of poorly structured input, short conclusion**: scanning gigabytes of logs, sifting reports, anonymising logs before a smart model sees them, commit messages, summaries and e-mails.</span> Quotably: *"you can do seppuku with Haiku."*
- **GPT = the second opinion.** Reach for it when the primary model has a hiccup — *"only when Claude feels sick"* — and as an independent reviewer family in a [[multi-model-review]] quorum.

<span style="color:red">**Same ladder, other vendors.** OpenAI now ships named tiers that map onto it ([GPT-5.6 Sol/Terra/Luna](https://github.blog/changelog/2026-07-09-openais-gpt-5-6-sol-terra-and-luna-are-now-available-in-github-copilot/)): **Sol ≈ Opus** (architecture, planning, long-horizon, review), **Terra/Codex ≈ Sonnet** (daily coding, scripts — Sonnet 5 and Codex 5.3 were neck and neck on [SWE-bench Verified](https://llm-stats.com/benchmarks/swe-bench-verified), 85.2% vs 85%), **Luna ≤ Haiku** (logs, mock-heavy coverage tests), and the newer [Astra](https://9to5mac.com/2026/09/22/openai-upgrading-chatgpt-and-codex-with-two-more-gpt-6-models/) ≈ Fable — too expensive to touch daily. Any model with **mini / flash / turbo** in its name "is for the boss's e-mail, not for code."</span>

<span style="color:red">**Gemini: research, not code.** Rooms agree on Gemini for web research and market research (Google search behind it) and never for coding — "Google never entered the coding-agent race." If it is the only model a client allows, feed it a long plan written by Opus, spoon-fed. Some companies ban Gemini, Grok and Chinese models on company code outright.</span>

## <span style="color:red">Choosing the tier for a task</span>

- <span style="color:red">**Feedback loop vs horizon.** A task with a systematic feedback loop (a test that passes or fails) can use a cheap model; **long-horizon** work, where a mistake surfaces days later, needs a strong one.</span>
- <span style="color:red">**Legacy: never below Opus.** On untested legacy code (1.5–3M-LOC codebases) "anything below the top tier is suicide" — Sonnet breaks it and there are no tests to catch it ([[legacy-migration-stages]]). Cheaper tiers are fine there for *read-only research*.</span>
- <span style="color:red">**Don't waste the architect.** *"Imagine asking a 15-year architect to write you some CSS."* Running Opus on a generated unit test or a new column is waste; planning and decomposing a feature is not.</span>
- <span style="color:red">**The weaker the model, the more precise the spec** — leave it little room, or "it isn't agentic work, it's babysitting a junior."</span>
- <span style="color:red">**Weak models cheat.** Tired or threatened, they falsely declare "all tests pass" ([[reward-hacking]], [[sycophancy]]). And model quality ≠ requirement coverage: on one search prompt a cheap GPT-5 mini went junior-grade (`findAll()` then filter in memory) yet implemented the diacritic-insensitive requirement Sonnet silently skipped — "a weak model landing the right answer is just luck" ([[advisor-vs-executor]]).</span>
- <span style="color:red">**Top tier for things that ripple:** dependency upgrades, design work like pagination, and editing specs (a changed decision must propagate across all spec files).</span>
- <span style="color:red">**Huge contexts need the big model:** an 8-hour transcript summarisation choked on Sonnet and worked on Opus with 1M context. Conversely, for a day of lab work Sonnet with ≈200k context is enough.</span>
- <span style="color:red">**Grill and brainstorm with Opus**, not Sonnet: interview output is short (a question is ≈200–300 tokens), so a whole [[grill-me]] session costs well under a dollar. Or warm your own brain up on a cheap model with no code access (problem space, business questions), then hand the conclusions to a Sonnet/Opus that looks at the code.</span>

## <span style="color:red">Review with a stronger tier than you coded with</span>

<span style="color:red">**Never review with the tier you coded with** — the reviewer must outrank the author. Because output costs ≈5× input, review is the cheapest place to spend the top tier: it reads a lot and writes little. One team had Opus refactor 2.5M lines in three weeks and the top tier review a 100k-line PR; on a 30-year-old EJB → Spring migration a Fable/Astra-class review (≈$1 per 5 seconds) found a transaction bug the seniors had missed. Then **fix with the cheap one** — "like juniors applying a tech lead's advice".</span>

<span style="color:red">**Cross-family review** catches what a single family shares: GPT reviewing Claude's code finds ≈20% more issues — "they fight like titans" — and on a Claude-only subscription a Copilot licence is the cheap way in. Models even smell each other: asked who wrote some code, GPT guesses "Opus" and roughly the version. Temperaments differ too: Codex bombards you with questions, Claude assumes decisions — comfortable, but risky while the domain is new (use [[plan-mode]] or [[grill-me]] to make it ask).</span>

## <span style="color:red">Plan with Opus, execute with Sonnet?</span>

<span style="color:red">The spring 2026 advice was *"scan with Opus, write code with Sonnet"*: let Opus plan into a markdown file, then `/model`-switch to Sonnet in the same conversation — "the art is a plan so detailed, made with Opus, that Sonnet has no choice but to do it right." By autumn the consensus had shifted:</span>
- <span style="color:red">**Don't restart in Sonnet after Opus has read the files** — you throw away the context Opus explored and pay for it a second time. Let Opus spawn Sonnet [[sub-agents]] for the laundry instead (pinned via the sub-agent's [`model` frontmatter](https://code.claude.com/docs/en/sub-agents#choose-a-model)).</span>
- <span style="color:red">**The split only pays on API pricing** (large seat counts or compliance constraints forcing an enterprise API contract). On a subscription, token prices keep falling — use Opus or above even on pet projects, and on a tight budget prefer **Opus at medium effort** over Sonnet: corrected slop costs more than the price difference.</span>
- <span style="color:red">The October variant: **grill → crafted prompt → fresh session.** After grilling, have the agent write a prompt holding all the discovered complexity, then run it in a fresh session — often a mid-tier model suffices ("enjoy a Sonnet").</span>

<span style="color:red">**Escalation, not just delegation.** Sonnet carries most of a task; when it gets stuck, escalate to Opus to think, then drop back. Claude Code packages this as the [advisor feature](https://code.claude.com/docs/en/advisor): a working model consults a stronger one on hard questions — one session saw Opus spawn a Fable to "ask the elder". (Unrelated to the Spring AI [[advisor]] — same word, different concept.) The reverse also appears: **Fable on high effort orchestrating Opus workers on medium and refusing to believe their reports** ("evidence inconclusive") — thorough, and one such session cost $593. A single self-hosted model has no escalation path at all.</span>

## <span style="color:red">Auto model routing is a box of chocolates</span>

<span style="color:red">"Auto" modes exist because they are cheap: they **silently route you to a cheaper model**, and [Copilot's auto selection](https://docs.github.com/copilot/concepts/auto-model-selection) routes on your **first prompt** — open with "hi" and you get the intern (Luna). The discount is only ≈10%, and after a 20-minute pause the router may re-choose. If output is unexpectedly weak, check which model actually answered; avoid Auto whenever you can anticipate the task's complexity, and never let a discount model touch staging. Want the top model? **Show the complexity**: a 2–4-page prompt in a fresh chat — or let a cheap model write that prompt from the ticket with all its design questions — gets you routed up; "trust me, it's hard" does not.</span>

## Local-model tiering

The emerging dream tier: **Opus as orchestrator delegating cheap subtasks to a company-server [[ollama|Qwen]]** — so you don't even burn Sonnet on trivial work like log scanning, support bots, or deterministic extraction. Local open-weight models (Qwen, DeepSeek) on company hardware offer data sovereignty *and* cost savings for the dumb-task layer. The constraint is quality: *"I won't maintain production code a local model wrote"* — reserve local tiers for scoped, verifiable tasks, never for judgement-heavy or long-horizon work.

<span style="color:red">**What the 2026 sessions measured:**</span>
- <span style="color:red">**The gap is real.** Open models (DeepSeek V4 / V4 Pro ≈ Sonnet, GLM ≈ Opus on paper, Kimi) score near the frontier on [SWE-bench](https://www.swebench.com/) at 2–10× lower prices, but run ≈6 months to a year behind in practice — an open model flailed for 40 minutes on a task the frontier tier did quickly; locally run coders were called "a monumental failure", good only for classification. Anthropic has documented [distillation attacks](https://www.anthropic.com/news/detecting-and-preventing-distillation-attacks) aimed at its models — a plausible reason for benchmark scores that outrun real-world quality; a claim in one session that these models were "trained illegally" is unproven.</span>
- <span style="color:red">**They don't know the harness.** Anthropic trains its models on Claude Code — "the model is born dreaming of the harness" — so a swapped-in model loses more than its benchmark gap. If you must swap, keep one harness and route models through [[claude-code-router]].</span>
- <span style="color:red">**Weaker safety alignment, not stronger.** Empathy jailbreaks ("my grandma worked in a bomb factory…") patched on frontier models still work on in-house or fine-tuned ones — "you're more exposed than with Sonnet." Many also lack **vision**, which locks them out of the UI/CSS feedback loop ([[uncensored-models]]). And nobody knows what is baked into their training data — a supply-chain-style risk.</span>
- <span style="color:red">**Hardware.** Self-hosting DeepSeek V4 Pro needs a ≈$0.5M machine; on a laptop it doesn't work, and an NVIDIA DGX Spark still costs more than renting what you consume. For fine-tuning, rent GPUs by the hour ([Vast.ai](https://vast.ai/)). Quantised open models on OpenRouter give "top model at degraded quality, budget price".</span>
- <span style="color:red">**Use them only for test-provable tasks** — subtle consistency bugs slip past them. The air-gapped workaround (plan with Opus at home, implement locally) is a borderline leak. Sovereignty without self-hosting: the same Opus weights run on Anthropic, Vertex, Azure and Bedrock, with the same quality under the same harness — but on API pricing ([[data-governance]]).</span>

## <span style="color:red">What the tiers cost</span>

<span style="color:red">List prices per MTok input/output as of October 2026 ([pricing](https://platform.claude.com/docs/en/about-claude/pricing)): **Fable 5.1** $10/$50, **Opus 5.5** $4/$20, **Sonnet 5.5** $2/$10, **Haiku 5.5** $0.10/$0.50 (up to 100K-token prompts). So Opus is ≈2× Sonnet, not the ≈20× rooms often guess. The previous generation: Opus 4.5–5 at $5/$25, Sonnet 4.5–4.6 at $3/$15, Haiku 4.5 at $1/$5 — half of Sonnet 5, not an eighth. Claude 4.6+ models (except Haiku 5.5) include the full 1M context at standard price. A circulating claim that "Opus 4.7 costs 15× Opus 4.6" is false: it kept the $5/$25 rate card, and only its new tokenizer maps the same text to ≈1.0–1.35× as many tokens ([Anthropic](https://www.anthropic.com/news/claude-opus-4-7)).</span>

<span style="color:red">On a subscription the constraint is quota, not price: Opus for all day-to-day coding reportedly burns a monthly quota in ≈3 days — that is what Sonnet is for. Moving to an API key typically costs several to tens of times the subscription for the same usage (published multipliers vary widely and are mostly unsourced — measure your own with `ccusage`; see [[token-economy]]). In Copilot, Opus counts ≈2× Sonnet and Haiku ≈½. Teams now estimate work as **time plus token dollars**, and inference prices keep falling fast — today's heroic token-saving is tomorrow's false economy.</span>

<span style="color:red">Effort is the second knob: medium to high by default, xhigh across system boundaries, max discouraged; changing it mid-session invalidates the cache. Details in [[effort-setting]].</span>

## <span style="color:red">Judging a model</span>

<span style="color:red">The eyeball benchmark: Simon Willison's [pelican riding a bicycle](https://simonwillison.net/2024/Aug/26/pelicans/) SVG — top models draw a recognisable one, mini models draw abstract art. [Artificial Analysis](https://artificialanalysis.ai/) compares intelligence, price and speed. But model evaluation for a product is statistical — hundreds of runs, then a [canary release](https://martinfowler.com/bliki/CanaryRelease.html) to 1–5% of users ([[agent-evals]]). Opus can also have "bad days" (serving issues): switch family when answers degrade. Only **1–2 people per team** need to chase the frontier and share what's provable ([AI champion role](https://academy.openai.com/public/clubs/champions-ecqup/resources/the-ai-champion-role)).</span>

## Why it matters

Picking the tier is a [[token-economy]] decision as much as a quality one: the architect-orchestrator (Opus) plans and delegates, while the cheap models execute the parallel grunt-work in [[sub-agents]] and the [[ralph-loop]]. A [[custom-agent]] can pin its own model (`haiku` for trivial steps, `opus` for long-horizon ones) so the tier choice is encoded, not improvised each run.

The tier is independent of the [[harness]]: the same Sonnet runs better inside a stronger harness, so "which model" and "which harness" are two separate knobs. Model strength also governs [[agent-skill]] activation — strong models wait to load a skill, weak ones greedily trash their own context.

## See also
- [[harness]]
- [[multi-model-review]]
- [[sub-agents]]
- [[ralph-loop]]
- [[custom-agent]]
- [[token-economy]]
- [[dumb-zone]]
- [[ollama]]
- [[data-governance]]
- <span style="color:red">[[effort-setting]]</span>
- <span style="color:red">[[claude-code-router]]</span>
- <span style="color:red">[[advisor-vs-executor]]</span>
- <span style="color:red">[[legacy-migration-stages]]</span>
- <span style="color:red">[[grill-me]]</span>
- <span style="color:red">[[uncensored-models]]</span>
- <span style="color:red">[[agent-evals]]</span>
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
- <span style="color:red">[[2026-07-03-ai-bank]]</span>
- <span style="color:red">[[2026-08-31-ai-software-vendor]]</span>
- <span style="color:red">[[2026-09-07-ai-bank]]</span>
- <span style="color:red">[[2026-09-25-ai-bank-migration]]</span>
- <span style="color:red">[[2026-10-09-ai-jax-london]]</span>
