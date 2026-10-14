---
title: Hallucination
category: concept
tags: [reliability, sycophancy, dumb-zone, prompts, drift, knowledge-cutoff]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-14-ai-industrial]]"]
created: 2026-06-11
updated: 2026-10-13
---

Hallucination is when a model confidently asserts something false or invented — and in agentic work it has five recurring, mostly *fixable* root causes.

## The five causes

1. **[[sycophancy]]** — the model is trained to please, so it tells you what you want to hear ("Boss, all tests pass!" — the ones it ran, after deleting the failing one).
2. **Vague or contradictory prompts** — we under-specify ("there's a bug here"), or our prompt **conflicts with a drifted [[claude-md]]** and the model silently resolves the tension instead of telling us we contradicted ourselves.
3. **Missing unspoken background** — better to let the agent **discover in the code** than to paste into a chat that compiles against the wrong library version and doesn't know your POM. Volunteer the things only you know: scale, request/sec, latency targets, load spikes, usage distribution.
4. **Term confusion** — synonyms or multi-meaning words ("transaction" = DB vs payment). Aggressively rename vague/confusing identifiers in the *code itself*. <span style="color:red">Then keep a **`glossary.md`** (or a glossary section in [[claude-md]]) and show it to the agent on every task — e.g. *client* = person with a national ID, *subscriber* = one SIM, *customer* = one-off buyer. It is semantic anti-drift for the AI and for the new colleague alike ([[2026-05-04-ai-telecom]], [[2026-09-14-ai-industrial]]). When several generations of an algorithm coexist, give each an uppercase marker name (WHITE / ELEPHANT / TIGER) or the agent drifts back to the textbook version it was trained on ([[2026-09-04-ai-software-company]]; details on [[claude-md]]).</span>
5. **The [[dumb-zone]]** — a [[context-window]] too full to attend reliably.

<span style="color:red">**Extra triggers** seen since: too many rules in memory files (one session put the tipping point at ≈85 — a heuristic); **SpecRot**, stale markdowns the agent finds in git and trusts ([[drift]]); and being *too close to something standard*, where the model pattern-matches the familiar version — the car-wash riddle ([[2026-06-02-ai-open-workshop]]). **Training-data drift** is a cousin: an agent kept resurrecting a `last_name` search because the original upstream open-source project in its training data searched by last name. Fix rule: *"before producing any artifact, verify against the actual code — don't assume"* ([[2026-05-04-ai-telecom]]). A [[dumb-zone]] symptom worth knowing: the agent labels the tests it just broke as "pre-existing failures" ([[2026-07-20-ai-telecom]]).</span>

<span style="color:red">Another session ranked the causes differently: (1) **insufficient context while you force its hand** — asked to extract a national ID number from a photo of the *back* of an ID card, it invented one; (2) the dumb zone; (3) a model too weak for the task ([[2026-08-06-ai-bank]]).</span>

A useful compression of the list names **three real causes** the operator most controls: the [[knowledge-cutoff]] (asking about facts newer than training), the [[dumb-zone]] (*"their head is just full"*), and [[sycophancy]] (*"all the tests pass — the ones I ran"*). Plain "it's just randomness" is a minor factor by comparison.

## <span style="color:red">Context, not prompt</span>

<span style="color:red">*"It's not stupid, it just doesn't know what you know."* When a recommendation looks wrong, the reflex is not "Opus is stupid" but **"what information do I have that it doesn't?"** — typically production volumetry ("10,000 owners in 2 months") it cannot know. Give it, and have it persist the fact in AGENTS.md on git ([[agents-md-discipline]]). Golden rule: **if a smart human couldn't do the job from the context you gave, neither can the AI** — don't ask for SQL tuning without the `EXPLAIN PLAN` or a pre-prod DB with realistic volumes ([[2026-05-25-ai-government]], [[2026-08-06-ai-bank]], [[2026-09-02-ai-insurer]]).</span>

<span style="color:red">**Give it tools to check reality instead of dictating the answer** ([[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]]):</span>
- <span style="color:red">Send it to the staging DB through an MCP (e.g. [DBHub](https://github.com/bytebase/dbhub) over Postgres; staging password in env, not git): "judge from real data samples, then come back". In one run all 4 text columns turned out nullable; in another, after one query it admitted unprompted that sorting by phone was useless.</span>
- <span style="color:red">"Show me the first 20 rows of the grid" — the data flows through its context, and only then does it *see*.</span>
- <span style="color:red">Breaking change? Extract a backend's clients from the [XML metadata Draw.io embeds](https://www.drawio.com/doc/faq/export-to-png) in exported SVG/PNG, not from OCR of arrows — the room caught a lying diagram that way.</span>
- <span style="color:red">Don't dictate ("I only want name and city") to "an architect with 40 years of experience"; nudge ("are you sure? think again"). Come with **questions, not fixed answers**: announce "let's paginate in the frontend" and it will sycophantically agree; always ask **"what do I lose by choosing option 1?"** ([[sycophancy]], [[answer-injection]]).</span>

## <span style="color:red">Make it prove its claims</span>

<span style="color:red">First move when the AI goes sideways: *"How did you come to this conclusion?"* — the root cause is almost always a vague prompt or missing context ([[2026-04-07-ai-product-team]], [[ai-spiraling]]). Then demand evidence the outside world can check:</span>

- <span style="color:red">**Proof links.** Not "give me sources" but *"give me Amazon links to the books and verify via web search that they point to real books"*; or "URLs I can verify that contain quotes of what you answered" ([[2026-04-07-ai-product-team]], [[2026-06-02-ai-open-workshop]]). Live lesson: Victor himself claimed a skill attribute `human-invoked: true` (the real one is `disable-model-invocation: true`); asking for a proof link exposed it — and the sub-agent asked to verify hallucinated too. *"Always demand the source, like asking a kid 'did you do your homework?'"* ([[2026-06-05-ai-consultancy]]).</span>
- <span style="color:red">**Grounding by citation.** *"Once you have a conclusion, search the document and tell me the line number and page where this exists."* If it can't cite, it didn't read — a chatbot "reviewing" a T&C page reported clauses that weren't there, silently falling back to general knowledge. Programmatic version: ask for quoted snippets, `grep` the source, reject answers whose quotes don't match ([[2026-05-06-ai-ecommerce]]).</span>
- <span style="color:red">**Proof, not status.** After "start everything", don't accept "done, it's running" — ask *"show me the HTML of the last page"*. The Doubting Thomas rule: re-run `mvn test` yourself ([[2026-05-06-ai-ecommerce]], [[2026-05-13-ai-it-services]]).</span>
- <span style="color:red">**Surface silent assumptions.** The worst bug is a requirement skipped without a word: an agent ignored "diacritic-insensitive" entirely; only *"what assumptions did you make?"* made it confess ("I assumed stored data is already normalized"). Start a complex requirement with a failing feature-file test that articulates it ([[2026-04-27-ai-healthcare-software]], [[assumptions-ledger]], [[acceptance-test-bdd]]).</span>

## <span style="color:red">Poisoned training data</span>

<span style="color:red">Some confident wrongness no prompt fixes. Researchers invented a fake disease, [bixonimania](https://www.smithsonianmag.com/smart-news/scientists-invented-a-disease-to-test-whether-ai-knew-it-was-fake-then-chatbots-started-saying-it-was-real-180988924/), to test whether AI would notice; major chatbots (Copilot, Gemini, ChatGPT) later described it as real ([[2026-09-10-testing-ai-bank]], [[2026-07-20-ai-telecom]]). The broader loop — LLM output feeding the next models' training data — is model collapse ([Shumailov et al. 2024, *Nature*](https://www.nature.com/articles/s41586-024-07566-y); [[2026-05-06-ai-ecommerce]]). Defence: the same proof links as above.</span>

## Defenses

Four of the five are inputs *you* control — so hallucination is largely an engineering problem, not a model defect. Curate the [[claude-md]] for contradictions, write a precise [[spec-driven-development|spec]], grill out missing context ([[grill-me]]), and stay out of the dumb zone. For facts newer than the training cutoff, don't trust recall — pull them in via WebFetch or [[context7]]. And review [[acceptance-test-bdd|acceptance tests]], because sycophancy makes the model's own unit tests untrustworthy. <span style="color:red">Add a home-memory line telling the agent to challenge ambiguous, contradictory or wrong prompts — "don't let me continue" — as an anti-sycophancy setting ([[2026-06-02-ai-open-workshop]]). Work in small steps and expect the first attempt to fall short: re-prompt when it took the right direction but stopped short; `/clear` and restart when it took the wrong *direction* ([[2026-09-10-testing-ai-bank]], [[token-economy]]). Text you queue while it works joins its current turn — [press Enter to queue](https://code.claude.com/docs/en/interactive-mode#queue-messages-while-claude-works) a correction instead of waiting ([[2026-09-02-ai-insurer]]).</span>

## See also
- [[sycophancy]]
- [[dumb-zone]]
- [[knowledge-cutoff]]
- [[claude-md]]
- [[context7]]
- [[grill-me]]
- [[acceptance-test-bdd]]
- <span style="color:red">[[ai-spiraling]]</span>
- <span style="color:red">[[answer-injection]]</span>
- <span style="color:red">[[assumptions-ledger]]</span>
- <span style="color:red">[[drift]]</span>
- <span style="color:red">[[feedback-loop]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
