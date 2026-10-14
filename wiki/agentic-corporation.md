---
title: Agentic Corporation (Blackboard / Dark Factory)
category: pattern
tags: [multi-agent, blackboard, async, issue-tracker, future-of-work, orchestration, dark-factory, team-structure]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-06-23
updated: 2026-10-13
---

The agentic corporation is the horizon where agents coordinate *through shared artifacts* rather than through humans — the issue tracker becomes a "blackboard" agents read from and write to asynchronously.

## The blackboard pattern

When an agent hits a genuine ambiguity it can't resolve, the move isn't to block on a human in the chat — it's to **post a clarifying comment on the issue** and move on. *"In a truly agentic corporation, another agent picks that up and answers asynchronously; the issue is just the blackboard."* The tracker (GitHub/Jira) is the shared memory: requirements, clarifications, and decisions accumulate there, where any agent or human can act on them later. This is why you **connect the agent to the issue tracker instead of retyping the ticket** (`i want to work on gh issue #1`) — see [[cli-vs-mcp-tradeoff]] for preferring the `gh` CLI over a heavier MCP.

The blackboard is the natural endpoint of [[grill-me|grilling]]: the questions a grill surfaces get posted for the business to answer async, rather than stalling the run.

<span style="color:red">**Team chat as the blackboard.** One AI-mature company **bans 1:1 DMs**: everything happens in team channels where a bot holds the full history (1+ year), answers *"what did we decide on the migration?"*, and distils decisions into knowledge and [[agent-skill|skill]] refinements ([[self-improving-knowledge]]). When someone is on leave, the team *plus the bot* still know. The cultural precondition — no DMs — is the hard part ([[2026-05-06-ai-ecommerce]], [[2026-05-11-ai-software-vendor]]).</span>

## <span style="color:red">The ticket board as the IDE</span>

<span style="color:red">The most concrete working example seen in a session (an energy-sector team): **dragging a ticket across columns triggers the next agent phase**. *Spec in review* → an MCP pulls the ticket and generates the spec files ([[spec-driven-development]]); *Implementation* → spec-driven execution; *Ready for review* → a fresh VDI spins up with the change deployed and an API client pre-loaded, so the approver reviews by clicking one URL from the train. Building it took ≈6 months of one engineer; features now ship in hours ([[2026-05-06-ai-ecommerce]]).</span>

<span style="color:red">A smaller chain of the same shape for bugs: an agent reads the ticket, reproduces it in a headless browser, writes a failing Playwright test, and hands off to a fix agent that turns it green — [[red-green-tdd]] wired across two agents.</span>

## The "dark factory"

<span style="color:red">The provocative real-world signal is **StrongDM's "Software Factory"** (Justin McCarthy, CTO, 6 Feb 2026, [factory.strongdm.ai](https://factory.strongdm.ai)), with public rules: *"Code must not be written by humans"*, *"Code must not be reviewed by humans"*, and *"If you haven't spent at least $1,000 on tokens today per human engineer, your software factory has room for improvement."* [Simon Willison's write-up](https://simonwillison.net/2026/Feb/7/software-factory/) (7 Feb 2026) linked it to Dan Shapiro's top "dark factory" level, and StrongDM's [corporate post](https://www.strongdm.com/blog/the-strongdm-software-factory-building-software-with-ai) (19 Feb 2026) sums it up as *"validation replaces code review"* — human review replaced by layers of automated scenario tests, lint, security and infra checks, not blind coding. StrongDM also sells the factory as a product; one showcase output is a vibe-coded Jira reimplementation.</span> The **"dark factory"** is the manufacturing metaphor (a lights-out factory with no humans on the floor). <span style="color:red">The reference taxonomy is Dan Shapiro's [*The five levels — from spicy autocomplete to the software factory*](https://www.danshapiro.com/blog/2026/01/the-five-levels-from-spicy-autocomplete-to-the-software-factory/): at the top level nobody reads code and the coder never sees the tests.</span>

<span style="color:red">**Timeline of "stop reading the code":** Feb 2025 Karpathy coins [vibe coding](https://x.com/karpathy/status/1886192184808149383) (everyone laughed) → Feb 2026 StrongDM (nobody laughed) → Apr 2026 Uncle Bob [announces](https://x.com/unclebobmartin/status/2080257779395154409) he no longer reads code. Victor's own position: *"I can't do that."*</span>

### <span style="color:red">Hidden acceptance tests — the anti-overfitting trick</span>

<span style="color:red">The part that makes a dark factory more than a slogan: **the acceptance tests are written by a different agent and hidden from the implementing agent** — they live in another repo, so the production agent can't read or edit them. Feedback to the coder is only *"you didn't satisfy requirement 7.3"*, like an ML **holdout set** or an informatics-olympiad grader, which makes overfitting to the tests (and [[reward-hacking]]) impossible. OpenAI's harness-engineering experiment adds another proof obligation: a **video of the bug before and after the fix**. See [[acceptance-test-bdd]] for the test side ([[2026-08-31-ai-software-vendor]], [[2026-09-30-ai-enterprise]]).</span>

### <span style="color:red">The road there — and why most teams stop at grey</span>

<span style="color:red">The "purgatory" path Victor describes: drive human review down **50% → 20% → 10% → dark**, restarting the loop with more pressure each time, over ≈9 months. It has been proven on greenfield — *"never seen it work in brownfield yet"* ([[2026-07-01-ai-java-community]]). Intermediate data points: one team reviews PRs **only through acceptance tests**, after 4–5 months hardening rules, tests and security first ([[2026-05-25-ai-government]]); by July 2026 two companies formally reviewed **<50% of PRs**, keeping critical files (OpenAPI, SQL, domain PlantUML) under CODEOWNERS ([[codeowners-elders]]).</span>

<span style="color:red">The counter-view: everyone dreams of the dark factory, nobody admits we need a **[[gray-factory|grey factory]]** where a human keeps one eye open; a full dark factory is *"plausibly a few years out"* ([[2026-08-10-ai-consultancy]]), though in July Victor expected the pattern to go mainstream within ≈9–12 months. On legacy, *"we just have to validate"* is exactly why dark factories fail — the team's domain knowledge is the scarce input, not typing ([[2026-09-25-ai-bank-migration]]).</span> Whether or not it generalizes, it marks the direction of travel the rest of this wiki guards against running blindly: it only works on top of disciplined [[spec-driven-development]], [[acceptance-test-bdd|acceptance tests]], [[multi-model-review]], and the anti-[[cognitive-debt]] safeguards — not raw [[vibe-coding]].

## <span style="color:red">What happens to teams</span>

- <span style="color:red">**2-pizza → "1-pizza" AI pods** (≈3–6 people including product and test): one person drives the whole flow and calls in 1–2 colleagues for a quarter-hour at the architecture stage. Juniors get review stamps and low-stakes/back-office projects, not the architecture phase. Victor dislikes coding solo, especially for design — pair or mob on `design.md` ([[mob-spec-review]]).</span>
- <span style="color:red">**Project management with AI**: it can size stories, find inter-dependencies, build a Gantt/Kanban — but that's high-impact output with no automated feedback loop, so use the "one writes, two review, then a human" pattern ([[multi-model-review]]).</span>
- <span style="color:red">**The next bottleneck after review and requirements is product ideation**, not coding — that's where job security moves (forward-deployed engineers; the term is Palantir's). Give the business its own agent wired to your sources so they [[grill-me|grill]] their own ideas — but not if they habitually underestimate your work (*"it took me a week to vibe it"*) ([[2026-08-10-ai-consultancy]], [[2026-08-06-ai-bank]]).</span>
- <span style="color:red">**The AI Champion** — the person who makes AI stick in a company ([OpenAI Academy](https://academy.openai.com/public/clubs/champions-ecqup/resources/the-ai-champion-role)). Caution from the same week: a champion must not build one factory for everyone ([[skills-governance]], [[agentic-reconversion]]).</span>

## Supervisor multi-agent layers

Even today's harnesses hint at it: an "Autopilot supervisor" that auto-approves an agent's choices is itself a supervisor-LLM multi-agent pattern — and a weak supervisor (a cheap model rubber-stamping sloppy reasoning — *"I smell Haiku!"*) is its failure mode. The same orchestrator/specialist structure powers [[sub-agents]] and the [[firefighter-agent]]; the agentic corporation is that structure scaled to a whole org.

## See also
- [[sub-agents]]
- [[cli-vs-mcp-tradeoff]]
- [[grill-me]]
- [[spec-driven-development]]
- [[cognitive-debt]]
- [[firefighter-agent]]
- [[model-hierarchy]]
- <span style="color:red">[[gray-factory]]</span>
- <span style="color:red">[[acceptance-test-bdd]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[reward-hacking]]</span>
- <span style="color:red">[[skills-governance]]</span>
- <span style="color:red">[[agentic-reconversion]]</span>
- [[2026-06-23-ai-device-maker]]
