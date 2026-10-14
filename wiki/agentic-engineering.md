---
title: Agentic Engineering
category: concept
tags: [agentic-engineering, discipline, autonomy, spec-driven, overview, adoption, productivity, careers]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-06-11
updated: 2026-10-13
---

Agentic engineering is the disciplined practice of building software *through* autonomous agents — investing human effort in specs, knowledge, guardrails, and review rather than in typing code — the deliberate opposite of [[vibe-coding]].

## The defining principle

**Autonomy scales with the engineer:** "the stronger the agentic engineer, the longer the flows you can run." A weak operator can supervise a single short prompt; a strong one front-loads enough thinking that an agent can run for hours unattended. <span style="color:red">The records grew through 2026: a 6-hour run that fixed a CI pipeline and waited 40 minutes for each build; **254 minutes, 189 tool calls and 19 sub-agents** for an architectural refactoring that pushed to CI and kept going; a 6-hour Java 8 → 25 migration; an ≈8-hour E2E optimisation; Victor's own 12-hour run on flaky tests that made a Jenkins build ≈20% faster; and a weekend-long spec on 20-minute builds that, a day and a half in, asked bash permission for a fork bomb ([[2026-04-07-ai-product-team]], [[2026-07-01-ai-java-community]], [[2026-07-20-ai-telecom]], [[2026-08-31-ai-software-vendor]]).</span> <span style="color:red">By September the framing changed: **maximum away-from-keyboard time is a vanity metric**. Groups claiming two days turned out to have one 40-minute build call running in a loop, and "no agent reasons continuously for two hours" ([[2026-09-04-ai-software-company]]; cf. [[ci-green-loop]]). What long runs really measure is the quality of the feedback loop: *"the more you have it work, the better feedback loops it means you gave it."* Waiting on CI or Playwright costs zero tokens.</span> Weekend-scale runs are not one loop but the [[ralph-loop]].

<span style="color:red">Give every task a **clear finish condition** ("give me the screenshots", "tests pass"). The agent loops on errors until it has the result, not until you say stop, and it may not say "done" until it has run every test it can, or you become its waiter ([[2026-04-07-ai-product-team]], [[2026-06-08-ai-security-tools]]; see [[chop-ai-waiter]]).</span>

## What the discipline consists of

<span style="color:red">What the AI needs from us fits on one drawing: clear **specs** (input/output), **guardrails** (permissions, hooks, code owners), an automated **feedback loop**, and **human review** at the end on the critical areas ([[2026-05-11-ai-software-vendor]]).</span> The day's whole arc is the toolkit:

- **Front-load thinking** — [[grill-me|grill]] the requirement, write a [[spec-driven-development|spec]], leave a [[handover]].
- **Manage context** — respect the [[token-economy]] and stay out of the [[dumb-zone]] (via [[sub-agents]], [[handover]], [[plan-mode]]).
- **Engineer the knowledge** — curate [[claude-md]], grow [[tripwire|tripwires]], share via [[cross-repo-knowledge]].
- **Constrain the output** — [[acceptance-test-bdd|acceptance tests]] from one side, design decisions from the other, then the [[static-analysis-gauntlet]] (machines draw blood first) and [[multi-model-review]] / [[code-review]] (sorted, judgement-only) where the human spends a fading attention budget. <span style="color:red">*"Put tests as fences at the boundary of the system, then tell AI to refactor inside safely"* ([[2026-04-07-ai-product-team]]).</span>
- **Guard the human** — beware [[ai-addiction]] and [[skill-erosion]].

The engineer becomes a "manager of agents", not a viper — see [[vibe-coding]] for the contrast.

**Two slogans capture the spine of the discipline.** First, *stop being the servant of the AI*: don't copy-paste between a chat and your editor (the [[chop-ai-waiter|CHOP anti-pattern]]) — integrate the agent and give it a [[feedback-loop]] so it checks its own work. <span style="color:red">"Ask mode is dying: it enslaves you to paste logs" ([[2026-09-24-ai-bank]]).</span> Second, *the new skill is seeing, testing, and reproducing the problem, not fixing it* — *"move your focus from typing to feeling the problems"* (races, deadlocks, load). The agent fixes almost anything once you point it at an observable loop; your job is to make the problem visible and to judge the result. Matching the right [[model-hierarchy]] tier to the task is part of that judgment.

**Every AI failure is an opportunity to enrich the plan or the context.** If the agent gets it wrong, the first question is: what did the agent not know (context), or what was the spec unclear about (plan)? Make "what did the agent learn this sprint?" the first item in every sprint retro — congratulate whoever added good insight to [[claude-md]]. Treat the agent like a junior who just joined: pair, babysit, show the way.

## <span style="color:red">Prompt with what hurts, not what to do</span>

- <span style="color:red">**Present the problem, not the solution**, to an Opus started in the project folder, *"like at confession: not what you want to happen, but what hurts"*, or like at a psychologist rather than with a junior ([[2026-08-06-ai-bank]], [[2026-08-13-ai-insurer]]). Prompts aren't sold anymore; the skill is stating the problem. Spend a minute on every task asking "how could an agent help me here?" ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**But know what you want.** *"Ich will"*: steer firmly, "like a shepherd", until the output is decent. Talk to it as a super-enthusiastic junior partner, in human language ("the little hand", not `cursor: pointer`), and put the rule in the folder's CLAUDE.md so it doesn't forget. *"The art of development now: how do you make something precise with so much endless randomness around you?"* ([[2026-05-04-ai-telecom]]).</span>
- <span style="color:red">**Don't structure the information for it.** Give it pictures, Confluence, code; tell it the story; it writes the markdown at the end ([[2026-08-06-ai-bank]]).</span>
- <span style="color:red">**Humble prompting.** Each time you outsmart the agent, ask **what information you had that it lacked, then grant access**. In one case you vetoed sorting by address because you had *seen the data*, so give it a DB CLI on staging or an anonymised read-only replica ([[2026-09-28-ai-gaming-tech]]). It teaches back, too: don't return Spring `Page` from REST, use `PagedModel` ([docs](https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html#core.web.page)).</span>
- <span style="color:red">**Give it every source a colleague uses**: Confluence, Jira via CLI, past PRs, CI failures, the running app via Playwright, meeting transcripts, chat and email, **production Grafana metrics (not logs)**, **all past post-mortems**, and production itself when exposure is small. Use staging, never production, for logs, DB and browser ([[2026-09-18-ai-agentic-how]], [[2026-09-28-ai-gaming-tech]], [[2026-10-06-gray-factory-devoxx]]).</span>
- <span style="color:red">**Cut its blast radius.** It tends to make app-wide what you asked for locally, and rework costs time, money and risk ([[2026-08-13-ai-insurer]]).</span>

## <span style="color:red">Working habits</span>

- <span style="color:red">**Never leave for a break, lunch or meeting without an agent working.** Hand it a fat task as you walk into the meeting; "Claude made meetings tolerable" ([[2026-07-20-ai-telecom]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-09-04-ai-software-company]], [[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Watch the first 2–3 minutes of thinking** to see which horse it backs, and stop it if wrong ([[2026-08-03-ai-telecom-ops-qa]]).</span>
- <span style="color:red">**Run agents in parallel.** Use ≈4 terminals as separate windows, not tabs, with a background colour hashed from the folder. Put agent-spawned terminals on a non-projected monitor, or open them in the background so they never steal focus. Parallelism is the skill in demand; one real interview format is *4 terminals, an impossible problem, one hour* ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]], [[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**Beware brain fry.** Pagination was 3–5 days of work in 2024; today it is ≈6 hours of hard thinking, because AI compresses days of thinking into hours. Recipe: **one hard task plus one polish task**, since 4 terminals of hard work leave you dead by 1 pm. Work in 15–20-minute "pills" between agents ([[2026-09-30-ai-enterprise]], [[2026-09-04-ai-software-company]]). "The new limit is your brain, not the tool" ([[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**YOLO is a necessity, not a vice.** *"If after 2.5 hours it asks 'may I?', you've lost the game"*. Agents must be autonomous, hence the sandbox ([[2026-07-20-ai-telecom]]; see [[docker-sandboxing]], [[approval-fatigue]]).</span>
- <span style="color:red">**Commit messages:** let the agent write them, one commit per task, with the problem and the reasoning, taking the Jira ID from the branch name ([[2026-10-01-ai-bank]]). The risk is that it takes you out of the loop on your own code. Counter-move: **you write the message, then "complete it — what did I miss?"** ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">**Polish UX live with the business in the room.** With an auto-restarting dev server (`node --watch`), they ask and the agent fixes under their eyes. The room found in 5 minutes what the agent missed in 9 hours of coding: a washed-out header font, a sort arrow that showed only on hover, jumping column widths ([[2026-09-16-ai-safety-critical]]).</span>

## <span style="color:red">Aim it at every pain, not just code</span>

<span style="color:red">**The DevEx ritual.** Every week or sprint, ask "what eats your time, brain and nerves?" (the three axes of the [DevEx survey](https://queue.acm.org/detail.cfm?id=3595878)) and hand each pain to the agent through a CLI or feedback loop it can close itself ([[2026-08-31-ai-software-vendor]], [[2026-09-24-ai-bank]], [[2026-09-28-ai-gaming-tech]]). It is also the answer when the business asks for AI ROI. Examples from the room:</span>

- <span style="color:red">Confluence search via a Python script over the REST API with a PAT; Azure DevOps work items and builds via [`az devops`](https://learn.microsoft.com/en-us/azure/devops/cli/); a DB via a vendor CLI ([mssql-cli](https://github.com/dbcli/mssql-cli)). One DB-CLI skill plus one prompt produced an Excel pie chart ([[2026-09-24-ai-bank]]).</span>
- <span style="color:red">CVEs and Sonar issues iterated until green; flaky tests found by grepping a year of CI failures; bloated Playwright suites; performance leaks (give it staging for the weekend) ([[2026-09-04-ai-software-company]], [[2026-09-24-ai-bank]]).</span>
- <span style="color:red">**Bureaucracy:** access forms and "justify why you need access" emails via browser automation; monthly-expiring admin rights renewed by a crontab script; a Playwright script that renews its own 90-day PAT ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-06-ai-bank]], [[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**Meetings:** capped at 35–45 minutes, prepared by a ≤5-sentence AI-drafted email or 5–7 agent-generated points, transcribed locally ([MLX Whisper Large v3 Turbo](https://huggingface.co/mlx-community/whisper-large-v3-turbo)), summarised, with Jira issues filed ([[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**Architecture board proposals** drafted by the agent from approved blueprints (text plus PlantUML/C4 DSL), then attacked by an adversarial critic from another model family. **Too many microservices with 30-minute deploys:** a script starts all 15 locally with tight `-Xmx` ([[2026-10-01-ai-bank]]).</span>
- <span style="color:red">House rule: **no human ever searches through a log again — only agents.** Watch the bill, though: one developer burned **$10k of tokens in a day** on a 2 GB production log ([[2026-09-04-ai-software-company]]). See [[ai-use-cases-beyond-coding]].</span>

<span style="color:red">**Spend spare quota on what you never had time for**: internal tooling, cleaned-up legacy, written tests, tuned pipelines ([[2026-08-13-ai-insurer]]).</span>

## <span style="color:red">Realistic gains — and their price</span>

- <span style="color:red">**Velocity.** Victor's estimates: ≈30% faster in 2026 ("50% is too optimistic"), and ≈1.3× on raw development speed but up to 2–3× on idea-to-production lead time. Either gain comes paired with time spent building CLAUDE.md, skills and feedback loops ([[2026-04-07-ai-product-team]], [[2026-07-09-ai-live-casino]]). A Goldman Sachs analysis found no economy-wide gain but ≈+30% in two areas, software development and customer support ([Fortune, Mar 2026](https://fortune.com/2026/03/03/goldman-earnings-ai-anxiety-no-meaningful-impact-productivity-economy-30-percent-in-2-areas/)), and the headline doesn't subtract token cost ([[2026-05-06-ai-ecommerce]]). One session cited a bank that had measured only **+1.8%** so far ([[2026-07-01-ai-java-community]]).</span>
- <span style="color:red">**Quality before quantity.** Tests, security and skills come first; "3× faster" arrives after ≈9 months of that groundwork ([[2026-05-13-ai-it-services]]). Field examples do exist: an agent profiled a Spring Boot suite and parallelised it from 35 minutes to **3 minutes** (4 JVMs × 3 threads) ([[2026-05-06-ai-ecommerce]]).</span>
- <span style="color:red">**Economics today.** One agent bug fix (tests, frontend, validation, commit, push) cost **≈$16 and ≈600 diff lines**, against ≈2 hours and ≈80 lines by hand. That is "not worth it *today*": AI writes ≈2× the test lines a human would, and a coverage gate makes it worse. But reasoning cost drops [≈10× per year](https://a16z.com/llmflation-llm-inference-cost/), so "next year you won't review 100% of the code" ([[2026-09-04-ai-software-company]]). Review is the #1 reported bottleneck ([[2026-09-28-ai-gaming-tech]]). **80% of Jira tickets are boring**, a candidate for a direct "Jira2PR" agent at ≈$2 with no ceremony ([[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**The "infinite token era" is ending.** Management should first ask "where do you lose most of your time and brains?", not "here are infinite tokens, ship more" ([[2026-07-01-ai-java-community]]; see [[token-economy]]).</span>
- <span style="color:red">**Where AI stalls: delicate semantics.** Transaction-rollback semantics needed ≈7 people plus AI to fix 1 of 6 cases. **Convergence = confidence**: review the scary cases ≈10–20× until the model *independently* proposes the fix you would have designed. In one case it was the very fix two seniors had designed a year earlier and never dared commit. Then trust it and capture it as a skill ([[2026-07-06-ai-medical-diagnostics]]).</span>
- <span style="color:red">Industry signals, anecdotal: engineers at frontier labs shipping ≈5 features to production per day and reading under half of the code ([[2026-05-13-ai-it-services]]); a team dropped into an unknown Python codebase that was productive **from day 2**, on par with the team that had owned it for 4 years ([[2026-08-13-ai-insurer]]). "Coding is solved" ([Boris Cherny](https://www.youtube.com/watch?v=SlGRN8jh2RI)). A 10k-LOC service fits in a 1M window; only its connections still need you ([[2026-09-28-ai-gaming-tech]]).</span>

## <span style="color:red">Rolling it out in an organisation</span>

- <span style="color:red">**Bottom-up, not top-down.** Top-down fails when the C-suite wants the future but nobody signs "I accept the business risk" ("dead on the table"). A separate silo doing agentic workflows, with no developer accountable for the production deployment, is sick. Start small on yourself: OpenSpec, grilling, code review. *"You cannot stop a developer from doing stuff. We are kings."* ([[2026-07-22-ai-agentic-how]]).</span>
- <span style="color:red">**Champions in the trenches.** Give Claude subscriptions to champions who collect colleagues' wishes and turn them into tools in an internal tooling repo; otherwise ten teams build the same tool. The AI lab must sit in the trenches, not in a cave with infinite budget. Agentify **testers and POs**, not only developers. Developers change the hardest, and are the first to react when AI blunders ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**Full adoption is the only mode.** Half-manual plus half-AI doesn't work. Make automation a daily habit (e.g. ≈1 automated test case per tester per week), roll out incrementally, one item each morning with a weekly follow-up, and tear down the QA↔dev wall: *"you can't run with AI faster than the team can comprehend"* ([[2026-07-06-ai-medical-diagnostics]]).</span>
- <span style="color:red">**Grow defences reactively.** ≈80% of tasks skip the spec/review circus; only 1–2 per sprint deserve an `architecture.md`. Add defences when you see slop ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">**Repay tech debt before the AI burst**: finish migrations, unify vocabulary, upgrade frameworks, harden acceptance tests ([[2026-09-04-ai-software-company]]). Shipping 3× faster exposes the next bottleneck the org never noticed: fuzzy requirements, slow builds, missing tests, legacy ([[2026-07-22-ai-agentic-how]]). "Agentifying yourself makes organisational pain points and technical debt much more apparent" ([[2026-07-01-ai-java-community]]). The biggest holes of 2026 are meetings, code review, unclear requirements (1–2 months between refinement and start), and waiting on silos ([[2026-07-20-ai-telecom]]).</span>
- <span style="color:red">**Measure carefully.** Stack Overflow's 2025 survey found 84% use or plan to use AI, but only 33% trust its accuracy ([survey](https://survey.stackoverflow.co/2025/ai)). Run a DevEx survey every 2 weeks ([DX](https://getdx.com/report/devex-productivity/)) ([[2026-08-27-ai-it-services]]). Mind Goodhart's law when AI usage becomes a KPI: some trackers already reject tickets that don't "smell of AI" ([[2026-08-06-ai-bank]], [[2026-10-01-ai-bank]]). Resistance is inevitable, so introduce change gently.</span>
- <span style="color:red">**Win AI-denying engineers by pairing** and showing what they can build, not by arguing, the way NASA's human computers moved on ([[2026-10-09-ai-jax-london]]). When the whole team panics, turn the agent into a teacher: Claude's [Learning output style](https://code.claude.com/docs/en/output-styles#learning) hands you the keyboard for the business logic. A `start app` skill **replaces a day of onboarding** ([[2026-10-06-gray-factory-devoxx]]).</span>
- <span style="color:red">**Report bugs to the tool vendor.** As an end user, it's the most valuable thing you can do. Maintainers of impactful open-source projects can apply for [Claude for OSS](https://claude.com/contact-sales/claude-for-oss) (6 months of Max 20x) ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">Company-code restrictions and the personal-subscription question are covered in [[data-governance]]; adoption patterns more broadly in [[ai-adoption]] and [[agentic-corporation]].</span>

## <span style="color:red">How the job changes</span>

<span style="color:red">**"Scrum is dead" → micro-waterfall.** Implementation is no longer the bottleneck: a sprint fits in a night, and *"the development cycle is two hours"*. When coding is "done", everything else catches fire: missing tests, a business that doesn't know what it wants, useless meetings. So push AI into analysis, testing and ops too. **Shift-left** is grilling the business; **shift-up** is architecture and the sequences between services; **shift-right** is acceptance, performance, resilience and security testing ([[2026-08-27-ai-it-services]], [[2026-09-14-ai-industrial]]). The business will do its own grilling after 3–4 sessions with you, and will ask for its own copilot once it notices your questions come from a terminal ([[2026-08-06-ai-bank]], [[2026-09-16-ai-safety-critical]]). End state: the business brainstorms with the agent without you; help them anyway ([[2026-09-18-ai-agentic-how]]). Eventually the business will run out of backlog, and product ideas will come from engineers ([[2026-09-30-ai-enterprise]]).</span>

<span style="color:red">**Roles that grow:**</span>

- <span style="color:red">**The one-pizza trio**, shipping ≥3× faster: a *viber* (often the PO) prototypes, a *developer* cleans the prototype up, and *QA* guards latency, resilience and quality. "Vibe fixer" is becoming a job title ([[2026-09-18-ai-agentic-how]]; see [[productionalize]], [[vibe-fixer]]).</span>
- <span style="color:red">**Forward-deployed engineer**: sit with a domain expert ("how do you want to see your patients?"), prototype on site in days, and let the agent glue client systems together. The term is Palantir's, from the late 2000s ([[2026-05-11-ai-software-vendor]], [[2026-09-28-ai-gaming-tech]], [[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">**The agent guardian**: someone who watches what the other agents do so they don't break production ([[2026-08-27-ai-it-services]]; see [[policeman-agent]]).</span>
- <span style="color:red">Also: *producting*, architecture, testing and ops; "personal software" for tiny businesses. SaaS gets fragile once agents can rebuild it ("SaaSocalypse"): Victor cloned a course platform in a morning ([[2026-09-14-ai-industrial]], [[2026-10-09-ai-jax-london]]).</span>

<span style="color:red">**The valuable engineer is a curious generalist and a good parent of agents.** "I'm back-end" is the lamest excuse; the narrow specialist is dead ([[2026-09-30-ai-enterprise]]). Standardisation covers only ≈30% of the path, and the rest is developer preference. Teams may shrink from 5 to 3 while doing the work of 8, but culture, politics and gut feel aren't automatable ([[2026-07-03-ai-bank]]). *"You're no longer the smartest in the room, not even when you're alone"*. The question is no longer "do we write tests?" but whether you are willing to review all the corner cases ([[2026-08-03-ai-telecom-ops-qa]]).</span>

<span style="color:red">**Learning becomes the job.** Junior → senior growth that took a decade must now happen in **3–6 months**, so book conscious learning time. *"Become a 10x learner before a 10x shipper"* ([[2026-09-04-ai-software-company]]). Use the techniques from *[Make It Stick](https://www.makeitstick.com/)* (active recall, spaced repetition, chunks of 3–4 elements) and free courses on [Anthropic Skilljar](https://anthropic.skilljar.com/) ([[2026-09-30-ai-enterprise]]). Delegating *all* thinking to AI erodes the analytical skill employers hire for ([[2026-10-01-ai-bank]]; see [[skill-erosion]]). Leave a legacy: share knowledge ≈1.5 hours a day ([[2026-07-09-ai-live-casino]]).</span>

<span style="color:red">**Hiring signal.** Give a candidate a real 2-hour task with a doc and a *weak* model. Afterwards a capable model evaluates *how they approached the problem* with AI: did they iterate, validate input, think about architecture? It does not grade the final code ([[2026-05-11-ai-software-vendor]]).</span>

<span style="color:red">People worth following: the Anthropic blog (others copy within months), Andrej Karpathy, Simon Willison (security), Steve Yegge, Matt Pocock ([skills](https://github.com/mattpocock/skills)), Dex Horthy ([12-factor agents](https://github.com/humanlayer/12-factor-agents)) ([[2026-08-03-ai-telecom-ops-qa]], [[2026-08-27-ai-it-services]]).</span>

As agents take on more, the human role shifts toward: specifying intent clearly, reviewing the result judiciously, and maintaining the judgment and craft that machines cannot supply. The production horizon includes [[firefighter-agent|firefighter agents]] recovering incidents autonomously before the human connects, and "all day in Claude Code" workflows where scoped MCP access to GitHub, Jira, CI, deploy tools, and Slack replaces the manual tool-switching. <span style="color:red">Slack channel bots turn requests into **multiplayer prompting**, 2–3 people driving one Opus ([[2026-08-31-ai-software-vendor]]; see [[chatops-agent]]).</span> The discomfort with this is emotional, not technical. <span style="color:red">*"We poured crap into production without AI too — now it's just easier"* ([[2026-09-30-ai-enterprise]]).</span>

## See also
- [[vibe-coding]]
- [[spec-driven-development]]
- [[ralph-loop]]
- [[token-economy]]
- [[multi-model-review]]
- [[skill-erosion]]
- [[firefighter-agent]]
- [[ci-green-loop]]
- [[feedback-loop]]
- [[chop-ai-waiter]]
- [[model-hierarchy]]
- <span style="color:red">[[ai-adoption]]</span>
- <span style="color:red">[[ai-use-cases-beyond-coding]]</span>
- <span style="color:red">[[agentic-corporation]]</span>
- <span style="color:red">[[productionalize]]</span>
- <span style="color:red">[[vibe-fixer]]</span>
- <span style="color:red">[[chatops-agent]]</span>
- <span style="color:red">[[data-governance]]</span>
- <span style="color:red">[[gray-factory]]</span>
- <span style="color:red">[[agentic-reconversion]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- <span style="color:red">[[2026-09-04-ai-software-company]]</span>
- <span style="color:red">[[2026-09-28-ai-gaming-tech]]</span>
