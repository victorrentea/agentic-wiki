# AI-Assisted Coding @ a consultancy — session notes

_generated in 9m_

Notes from our 2-day workshop on AI-assisted engineering. **Day 1** covered the economics of running agents (models, tokens, context, permissions), then the two hands that squeeze hallucination out of the SDLC: specs on one side, requirement-level tests on the other — plus the guardrails and metrics that keep an agent honest when nobody is reading every line anymore. **Day 2** went into spec-driven development end to end, the agentic workflow around it — adversarial review, guided human review, closing the feedback loop into your markdowns — and finished on AI security: prompt injection, supply-chain attacks, and how to actually contain an agent.

Keep the links: several are the exact tools we demoed live.

# 📅 Day 1 — 2026-08-10

## The dark factory and the new bottleneck

1. Dark factory = requirements in, production deploys out, no human in the loop — management's dream, plausibly a few years out.
2. Writing code stopped being the bottleneck; **code review is**. Optimize there, not in typing speed.
3. New job forming: "make my vibed dream production-ready". ⚓ [vibe-code fixer](https://www.linkedin.com/jobs/search/?keywords=vibe%20code%20fixer)
4. Only 1–2 people per team should chase the [AI frontier](https://academy.openai.com/public/clubs/champions-ecqup/resources/the-ai-champion-role) — share when you have something provable, not as a daily activity for everyone.

## What is actually in the system prompt

5. Everything above your first message: harness dynamic prompt, global + project [CLAUDE.md](https://code.claude.com/docs/en/memory), memory, tool/MCP schemas, and skill *front-matter descriptions only*.
6. The harness dynamic prompt is what makes Claude Code better than the competition — not the model alone.
7. [Hooks](https://code.claude.com/docs/en/hooks) are never sent to the LLM — they react programmatically to harness events. Same for path-scoped rules.
8. ["Rules"](https://code.claude.com/docs/en/memory#path-specific-rules) = a [skill](https://code.claude.com/docs/en/skills) with a path glob in front-matter; its body is auto-injected before reads/writes of matching files.
9. Team knowledge belongs in a shared git repo / plugin marketplace — skills, MCPs, hooks, team CLAUDE.md — never in a personal `~/.claude`. ⚓ seven microservices, one team

## Tool definitions are a token tax

10. Every MCP tool ships its name + full schema + param descriptions into **every** conversation turn. ⚓ Grafana 65 tools ≈ 10k tokens
11. Turn on the [tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool): only names load, schemas fetch lazily on demand.
12. Prefer a CLI + a skill that teaches it over an [MCP](https://code.claude.com/docs/en/mcp) — no JSON schemas, and `jq` compresses the output. ⚓ fork the Jira MCP into a CLI
13. Fetch a ticket once, dump it to markdown on disk, then grep the file instead of re-calling the MCP.
14. Do write-backs (updating Jira) inside a [subagent](https://code.claude.com/docs/en/sub-agents), so the garbage context dies with it.

## Subscription vs API, and model choice per phase

15. API billing costs 10–30× a subscription — gym-subscription effect plus subsidized pricing. ⚓ $2600 API vs $90 sub
16. Never put a personal subscription on employer work — no NDA cover. ⚓ a guy got in trouble
17. Match the model to the phase:
    - top-tier (Fable-class): legacy, architecture, multi-service migration plans
    - Opus: plan / spec markdowns / review
    - Sonnet: coding, but only *after* a detailed spec
    - GPT/Codex: second reviewer (≈+20% findings) and fallback when Claude has a bad day
    - Gemini: internet research
18. Don't restart in Sonnet after Opus already read the files — you'd throw away context you paid for; let Opus spawn Sonnet subagents instead.
19. Effort: medium for coding, high/x-high for planning, never max.
20. [Claude Code Router](https://github.com/musistudio/claude-code-router) drives any backend model from the Claude Code harness — the open-weights insurance policy. ⚓ Opus UI, GPT underneath

## Token efficiency that measurably works

21. `/clear` between unrelated tasks — otherwise the previous task's knowledge is dragged into the new one.
22. Reply to a big-context session before the [prompt cache](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) expires; go idle and the whole context is re-billed at full price. ⚓ "hi" on 900k = $20 vs $0.50
23. Shrink what is re-sent every turn:
    - trim CLAUDE.md
    - remove unused MCP servers
    - disable unused plugins
    - enable tool search
    - don't install 60–80 skills
24. Don't force subagents — each reloads the system prompt and may re-open files you already paid for.
25. Reading files is where the money actually goes — file reads, not your prompts, are the biggest token sink.
26. Measured, not advertised — JetBrains benchmarked the token-saver tools themselves:
    - [RTK](https://github.com/rtk-ai/rtk): [**+7.6% more expensive** at low effort, +0.1% at high](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings/) — "at no point did rtk *save* anything", against an advertised 60–90%
    - RTK's own dashboard claimed 96.2M tokens saved on the same runs where the bill went up — it counts a baseline that never existed
    - [caveman](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/): ≈8.5% on real agentic runs, not the 65% claimed on prose
    - Headroom: a net loss
27. Put cost in the status line — model, effort, context used, 5h quota, turn and session cost. A two-digit session cost is the signal to stop and ask what happened.

## Context rot, compaction, subagents

28. Alarm thresholds: ≈150k tokens for Sonnet, ≈600k for Opus — past that expect hallucination and sycophancy.
29. Tool calls and their results are ≈95% of context; thinking blocks are flushed at end of turn — which is why the agent writes itself a long summary and cannot recall what it thought.
30. Prefer explicit compaction (write markdown, review it, fresh session) over `/compact` — unclear what survives, especially loaded skill bodies.
31. [Subagents](https://code.claude.com/docs/en/sub-agents) read 80k tokens and return 100 lines — useless files never touch the orchestrator's context. Warn it up front that an area is hairy so it sends explorers first. ⚓ people have died where I'm sending you

## Permissions are broken — contain outside the agent

32. [`permissions.deny`](https://code.claude.com/docs/en/permissions) on Bash is trivially bypassed: the same command with an extra flag, a message, or a `cd` prefix slips straight through. ⚓ git commit --no-verify
33. Deny only works at whole-tool granularity (WebFetch, skill loading) — never on `Bash(...)` patterns.
34. Real containment lives outside the agent: restrict its credentials and enforce protections server-side.
35. Auto mode is the sane default — a Sonnet classifier judges intent from human/AI messages only, with no tool noise in its context. ⚓ [fork bomb](https://en.wikipedia.org/wiki/Fork_bomb) approved

## Shift left: kill the ticket, grill the requirements

36. A ticket with only a title is **a declaration of war** — shovel it into an agent and code review becomes hell. ⚓ declaration of war
37. Two hands squeeze out hallucination: the spec reaching down into anticipated quirks, and acceptance tests reaching up into the same places.
38. [Grilling](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling) prompt shape:
    - one question at a time, wait for the answer, walk the decision tree
    - always attach the agent's own recommended answer
    - let it explore env/files/tools instead of asking, but keep decisions human
    - functional questions (PO) → acceptance criteria (QA) → technical, so business can leave early
39. Devs typically surface ≈7 questions, the AI 15–25. ⚓ QA lead found 30
40. Saying no to the business is the job — that is what you were hired for. ⚓ sort a grid by two pets
41. Use spec-driven development for anything that would take more than a day by hand; raw prompting is a gamble. Grilling first, then [OpenSpec](https://github.com/Fission-AI/OpenSpec).

## Spec markdowns: lifecycle

42. Write the design doc in the team's ubiquitous language — it *is* the architecture spec these frameworks want.
43. For high-stakes features, have a second agent in a *separate* conversation criticize the spec.
44. Delete spec markdowns after ≈3 months so agents stop trusting obsolete decisions. ⚓ history heresy
45. Before deleting, split them: functional → Jira, architecture → one-line [ADRs](https://adr.github.io/) in `architecture.md`, acceptance criteria → tests, `tasks.md` → trash.
46. An ADR for an AI can be a single line — it already knows the tech, only the project-specific choice matters. Keep CLAUDE.md short and behavioral. ⚓ Boris Cherny's 60 lines

## Which tests deserve human review

47. Do **not** review AI-generated mock-based unit tests — they exist so the agent can build bottom-up cheaply. Re-check that assumption at every model upgrade.
48. Mocks are still hated for three reasons: coupled to implementation, unreadable, and they lie when reality drifts.
49. Review only tests close to the requirements: Gherkin if you have a PO to sit with, otherwise a [terse testing DSL](https://github.com/victorrentea/unit-testing/blob/master/src/test/java/victor/testing/groceries/GroceriesMessageHandlerTest.java) with high signal-to-noise.
50. [Gherkin](https://cucumber.io/docs/gherkin/reference/)'s weak spot is the glue code — read the step definitions or you get cheated assertions. ⚓ inserted ACB, asserted ABC
51. BDD failed on a false dream: business was never supposed to *author* feature files — devs write them, business validates them line by line.
52. Place ≈80% black-box at the API boundary of one microservice / module / bounded context — [WireMock](https://wiremock.org/) and [Testcontainers](https://testcontainers.com/) allowed, mocks not.
53. Full black-box coverage is combinatorially impossible: extract the 40-branch algorithm into a near-static pure function and table-test it. ⚓ the tall buildings

## Stopping agents from cheating

54. Keep acceptance tests in a **separate repo the coding agent never sees** — on failure it learns which requirement is unmet, not which assertion to tweak. ⚓ professor's hidden test suite
55. AI optimizes for pleasing you and cuts corners as context fills: "all tests green" means "the ones I chose to run". ⚓ those were already failing
56. Confidence in black-box tests is what makes "flush the microservice and regenerate it with the next model" a rational plan. ⚓ 10,000 Python tests vs a Java service
57. E2E tests mostly wait on other systems — parallelize until the CPU pins, partitioning data by session/tenant so runs don't overlap.
58. 20 near-identical tests sharing 5% differences is an acceptable DRY violation — it mirrors production's blast radius. When a shared change breaks all 20, hand the failures to an agent and review only the fix.

## Architecture guardrails agents cannot dodge

59. Keep a PlantUML package diagram and assert it against the real code with [ArchUnit](https://www.archunit.org/) — delete a line from the diagram and the build goes red ([PackagesArchTest.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/PackagesArchTest.java), [packages.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/packages.puml)).
60. The agent's only escape hatch is deleting the guardrail test — so cover those paths with [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS) and the PR summons the architect. ⚓ the elders awaken
61. **Never ask AI to draw a diagram.** Generate a scripted projection of the code that cannot go out of sync.
62. Observed sequence diagrams: tag a critical Gherkin scenario, collect 100% of [OTel traces](https://opentelemetry.io/docs/concepts/signals/traces/) during the E2E run, render PlantUML from Grafana — an *observed*, not designed, diagram.
63. Those diagrams prove runtime facts a review cannot: a cache hit, N repeated calls to one API, broken trace propagation. ⚓ fixed a race condition from a diagram
64. Diff the generated diagrams (sequence, package, DB schema) between PR and main to review a change visually. ⚓ VET_ID foreign key added

## Metrics that point at dangerous code

65. Surveil where AI-generated code went ugly with cheap computable signals:
    - file size, cyclomatic and [cognitive complexity](https://www.sonarsource.com/resources/cognitive-complexity/)
    - incoming vs outgoing coupling, plus instability
    - number of public methods = public surface (1 relaxed, 20 uh-oh)
    - distinct committers, commit churn, bug-fix commit count (parse the Jira ID)
    - low coverage *from requirement-level tests*, not unit tests
66. Both coupling directions hurt, for opposite reasons: high incoming = huge blast radius, high outgoing = fragile. ⚓ class Util vs facade
67. Correlate change cadence across services — three microservices that always change together should probably deploy together.
68. [Code City](https://wettel.github.io/codecity.html) renders each file as a building (footprint = size, height = complexity, color = churn) — good for first-contact orientation in an unknown codebase, nothing more. ⚓ Spring's DefaultListableBeanFactory, tall and red

## Legacy refactoring playbook

69. Opus-class model, 1M context, high/x-high effort only — never Sonnet, never medium, on legacy.
70. Step 1 is tests, not refactoring: grow existing unit tests into boundary-level functional tests until ≈85% coverage comes from those alone; [PIT mutation testing](https://pitest.org/) on the critical domain classes.
71. Constrain scope hard — a minimum viable vertical slice plus an explicit "what NOT to do" chapter, because AI always upsells. ⚓ copy the logic byte-perfect
72. Have the agent generate [OpenRewrite](https://docs.openrewrite.org/recipes) recipes for repeating syntax cleanups: syntax-exact, zero tokens per run ([InlineSingleUseLocalVariableTest.java](https://github.com/victorrentea/petclinic/blob/main/refactoring-legacy/src/test/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariableTest.java)).
73. Finish abandoned migrations even when it looks like burning tokens for no business value — mixed paradigms wreck the agent's model of the code. ⚓ tiles-to-Angular, half done

## Optimizing code review

74. Layer the defenses before a human looks: [`/code-review`](https://code.claude.com/docs/en/code-review) and `/simplify` in a *separate* conversation, then Sonar for quality and Snyk for security.
75. Two agents = two dice rolls; slop surviving both is orders of magnitude less likely. ⚓ coin landing on its edge twice
76. For maximum blood, have GPT review Opus's work — they hate each other.
77. Never review alone: ask the agent what it was least confident about, what it assumed, and what the reviewer objected to — sorted descending by risk.
78. Make it introduce the PR gently — diagrams and a clickable markdown map before any code.
79. 30–40 minutes is the attention budget; a PR needing more is too big. Slice into stacked PRs of ≈200 lines each, approved one by one with fixes merged forward.
80. Reviewing the top ≈50% by criticality is a legitimate steady state — review carefully until you see ≈20 consecutive impeccable PRs in one area. ⚓ [Uncle Bob stopped reviewing](https://x.com/unclebobmartin/status/2044114698451476492), Victor didn't
81. Check your rejections are team-accepted reasons, not personal taste. Clean code now matters for *your* bandwidth, not the agent's.

## Fatigue-LGTM, skill erosion, closing the loop

82. The risk of the year: approving agent proposals brain-dead, because of the sheer volume of questions and code. ⚓ dog in the burning room
83. You are no longer the expert in the room — even if you are alone in it. Never "looks good to me" on something you don't fully understand.
84. When a spec contains a concept nobody understands, stop the review and turn it into a 10–30 min learning session. Have the agent quiz you, or scaffold a <200-line throwaway prototype.
85. If you had a better idea than the agent, that is *your* failure to give context — then add the missing line to CLAUDE.md / a skill / a rule. ⚓ 0.2 EUR per line
86. Monthly, feed human PR comments and spec decisions back into review bot rules, linter/Sonar rules, CLAUDE.md and subagent definitions — the human reinforcement loop that gets you to half-reviewed code in ≈9 months.
87. Anything with a mechanical feedback loop is already solved: load tests, JFR profiling, heap dumps, build-time optimization. Hand it the artifact and let it loop for days. ⚓ 17 heap dumps destroyed

## 🎯 Main takeaways

- **Code review is the bottleneck now** — every technique on day 1 exists to reduce how much code a human must read.
- **Specs and requirement-level tests are the two hands** that leave hallucination nowhere to land; unit tests full of mocks are the agent's scratchpad, not your review surface.
- **Guardrails must be scripted, not asked for** — ArchUnit against a diagram, generated diagrams from traces, CODEOWNERS on the guardrail files. The agent cannot argue with a script.
- **Permissions in settings.json do not contain an agent** — restrict credentials and enforce server-side; use auto mode for the day-to-day.
- **Tokens are an engineering concern**: subscription over API, `/clear` between tasks, tool search on, unused MCPs off — and put the running cost where you can see it.
- **You are not the expert in the room anymore** 😢 — the counter to fatigue-LGTM is mobbing, quizzes, and writing the lesson back into markdown.

## Corrections ❌

- Victor said **Boris Cherny created Claude** ❌ — Anthropic calls him the [inventor and Head of *Claude Code*](https://www.anthropic.com/webinars/claude-code-for-financial-services-boris-cherny), not a creator of the Claude models.
- Victor said **slash commands died and were replaced by skills** ❌ — [the docs](https://code.claude.com/docs/en/skills) say custom commands "have been merged into skills" and "your existing `.claude/commands/` files keep working".
- Victor said **four ifs give you four branches** ❌ — [cyclomatic complexity is "1 + number of conditional branches"](https://docs.sonarsource.com/sonarqube-server/user-guide/code-metrics/metrics-definition), so four decision points is 5, and four sequential ifs give up to 16 distinct paths. The point (complexity ≈ minimum number of tests) still stands.
- Victor said **Quarkus starts 6× faster and eats 6× less memory than Spring Boot** ❌ — that order of magnitude belongs to GraalVM *native images*, not the JVM. An [independent Java 25 startup benchmark](https://gillius.org/blog/2025/10/java-25-framework-startup.html) measured Spring Boot at 1.91 s vs Quarkus at 1.15 s on the JVM — ≈1.6×, not 6×. ([native-mode comparison](https://dev.to/bruno_minato/from-jvm-to-native-my-performance-experiments-with-spring-boot-and-quarkus-2je2))
- Victor said **embedded Postgres runs in memory** ❌ — those libraries use ["native postgres binaries running directly on the target platform"](https://github.com/zonkyio/embedded-postgres), unpacked into a temp directory (`/tmp/embedded-pg/PG-XYZ`).

# 📅 Day 2 — 2026-08-11

## Direct prompting vs plan mode vs spec-driven

88. The dial: minutes of work → just prompt; about a day or unknown territory → plan/brainstorm first; more than 1–2 days → spec-driven is mandatory.
89. In horrific legacy, never start any task without first ordering a study of the terrain by sub-agents.
90. Specs are the artifact worth reviewing cold — sleep on them, or review async. Plan-mode output is volatile and disappears.
91. Let the agent judge task size and pick the technique, rather than humans routing every ticket.

## Working a spec-driven change

92. Keep `proposal.md` non-technical so the PO can read it; status codes, batch sizes and JSON belong in `design.md`. ⚓ "Who's Jason?"
93. Each artifact has its own audience: proposal → business, design → engineers, acceptance criteria → the tester in the room.
94. The "rejected / not included" section is double value — it blocks feature creep *and* upsells the next ticket.
95. Never hand-edit generated markdowns: instruct the agent, and have it fix its own framework prompts so the mistake stops recurring.
96. Commit the framework's markdowns and tailor them — your own in-house plugin included. ⚓ untouched after six months = never made it yours
97. Mine published elicitation prompt lists for review personas rather than inventing them — [BMAD's advanced elicitation](https://docs.bmad-method.org/explanation/advanced-elicitation/) is a ready-made set. ⚓ "assume the project failed, work backwards"

## Spec review without fatigue-LGTM

98. One wrong line in 220 hides real bugs, and a room of 27 people misses it even when told where to look. ⚓ Potter, Harry
99. Beat the fatigue by assigning paragraphs to different voices — each person narrates their section aloud and the group confirms.
100. Phrase review comments as genuine doubt, never pressure — "make no mistakes" prompts push the agent into sycophancy.
101. When the spec leans on a technique the team doesn't understand, stop and have the agent scaffold a ≈200-line runnable demo. ⚓ Hibernate @BatchSize
102. Every grilling question you answer is a diagnostic — ask what knowledge the agent lacked, then store it. ⚓ 10,000 owners from business

## CLAUDE.md hygiene and progressive disclosure

103. Everyone adds freely; the team prunes together every two weeks — cap around 150 lines.
104. Delete anything the model already knows, anything a linter or quality gate can enforce, and anything derivable from the code. ⚓ endpoint list drifts
105. When a section passes ≈20–30 lines, push it down a level:
     - subfolder CLAUDE.md, loaded on demand
     - skills (the agent may skip them)
     - rules bound to file paths (forcibly injected)
     - referenced markdown files
106. Recurring cross-feature concerns belong in a skill, not in CLAUDE.md. ⚓ a "grid" skill

## Connect the agent to every source

107. Every time you feel like copy-pasting into the agent, connect it to that source instead.
108. Give it code, the staging DB, and production *metrics* — RPS and table sizes are derivable from exposed metrics alone.
109. Meeting transcripts need no corporate MCP approval — a local Whisper model on your laptop is enough. ⚓ just ask the room
110. Two [MCP](https://modelcontextprotocol.io/specification) features worth knowing: **sampling** (your server calls back into the client's LLM, so Haiku can summarize a Jira issue before it costs you context) and **elicitation** (the tool forces a human confirmation prompt, built into the protocol).

## Adversarial review and guided human review

111. The reviewer must run in a *separate conversation* so it isn't anchored by its own prior decisions — accept that this doubles the reading cost.
112. Never review with the tier you coded with: Opus or GPT only. ⚓ GPT reviewing Opus = blood
113. Two streams decide what a human reads first: findings the reviewer raised and the coder dismissed, and the places the coder itself was uncertain.
114. Make the coder emit an `assumptions taken` markdown with a 0–1 confidence per item, sorted ascending.
115. Everyone dreams of the dark factory; nobody admits we need a **grey factory** where a human keeps one eye open.
116. Keep a scratch agent open beside you while reviewing — every hunch becomes a spawned investigation instead of a sidetrack.

## Close the loop and upgrade the markdowns

117. The agent isn't done at push — have it wait on CI, merge dev into the PR and resolve conflicts before tagging you.
118. Every time slop reaches you, ask which markdown let it through, then upgrade *that* one:
     - CLAUDE.md and skills for small local gaps
     - the adversarial reviewer agents (add perf, volumetry, pentest lenses)
     - the architecture/task markdowns when the failure was structural
     - back to Jira when the business missed a case
119. Add a `/feedback` command that inspects the conversation for anger and corrections, then opens a PR against the framework repo.
120. Spend weekend and idle quota on build speed, flaky tests and more acceptance tests — never on risky refactors.

## Where this is heading

121. "Spec the day, work the night": refine with business by day, launch long implementations at 5pm, review in the morning.
122. Move the framework into cloud agents — the agent tags you on the PR when the architecture is cooked, and you reply in PR comments from a phone. ⚓ laptop half-open on the metro
123. Give the business its own agent wired to your sources so they grill their own ideas — but not if they habitually underestimate you. ⚓ "it took me a week to vibe it"
124. Once review and requirements are optimized, the next bottleneck is **product ideation**, not coding — which is where the job security moves. ⚓ forward deployed engineers

## AI security: how agents actually cause damage

125. The canonical incident: an agent hit expired credentials, hunted the repo, found a stray `.env` with an over-scoped key — and dropped the prod DB plus three months of backups. ⚓ prod + backups gone
126. Root cause is always an over-permissioned key — someone ticked every checkbox creating a classic [GitHub PAT](https://docs.github.com/en/authentication/keeping-your-account-secure/managing-your-personal-access-tokens).
127. A CLI the agent invokes cannot ask a human for approval — the destructive command runs unattended.
128. Never do ops work with a weak model or a bloated context: Opus, 1M, high reasoning, under 500k used.

## Credential isolation

129. Your terminal's `gh` and cloud logins impersonate you completely — an agent inheriting them inherits all your permissions.
130. Give the agent its own narrow keys inside a container; keep your personal keys on the host.
131. Audit what keys the agent can reach on a schedule, like a disaster-recovery drill — ask a *weaker* model in plan mode to enumerate them and what damage they'd allow (Opus refuses the question as hacking).
132. Best-in-class sandboxes keep real credentials outside the VM entirely and substitute them at egress. ⚓ fake key swapped on the way out

## Prompt injection: the core problem

133. **Agents cannot distinguish task from content** — anything they read can be read as instructions. This is the whole security story in one line.
134. The vectors that matter:
    - web pages fetched with `curl` — hidden instructions in HTML comments; use a real fetch tool or [Context7](https://github.com/upstash/context7) instead (also far cheaper)
    - downloaded skills — a malicious markdown prompt is worse than a bundled shell script, because it commands your whole agent
    - MCP servers — tool *descriptions* enter context without ever being called, and `npx`/`uvx` runs their code on your machine
    - injected user data — reaches your logs, and the agent reading logs to debug an incident is the most dangerous reader of all ⚓ user address = "forget all instructions"
    - RAG poisoning — a Confluence with 10k pages by 5k authors, some of them fired and angry, is an unvetted injection surface
135. [Snyk's ToxicSkills audit](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/) scanned 3,984 marketplace skills (Feb 2026):
     - **36.8%** (1,467) carry at least one security flaw
     - **13.4%** (534) have a critical-level issue — malware, prompt injection, exposed secrets
     - **76** confirmed malicious payloads, built for credential theft and data exfiltration
136. Nobody curates these marketplaces ([skills.sh](https://www.skills.sh/) was one of the two Snyk scanned) — read every skill at least diagonally before it enters the company. ⚓ npm and PyPI, all over again
137. Scan a skill before it lands: [NVIDIA SkillSpector](https://github.com/nvidia/skillspector) checks 68 vulnerability patterns across 17 categories — prompt injection, data exfiltration, privilege escalation, supply-chain risk — for Claude Code, Codex and MCP skills.
138. A real worm propagated by committing a poisoned CLAUDE.md into repos, infecting teammates' agents. ⚓ worm via CLAUDE.md

## Asking nicely does not work

139. Never rely on CLAUDE.md or a system prompt to forbid harm — the only real restriction is a permission you withheld.
140. A computed [adversarial suffix](https://llm-attacks.org/) flips a model with no visible change, exactly as a perturbation flips an image classifier. ⚓ German Shepherd → corkscrew
141. Frontier models screen prompts with a separate classifier, but a freshly computed suffix is unknown to it — local and small models have no such defense at all.
142. Red-team your own setup on a schedule: ask the agent to escape the sandbox, delete `~/.bashrc`, take down staging.

## Running agents safely

143. Auto-mode — a second LLM approving the first's actions — is the only permission mechanism that actually holds.
144. Never start an agent in your home directory: that one "trust all files in this folder?" covers `~/Downloads` and your SSH keys. ⚓ one yes, all your keys
145. Run it in Docker with a single mounted folder and an egress allowlist — no Docker socket, no privileged mode; prefer a [Docker Sandbox](https://docs.docker.com/ai/sandboxes/) micro-VM for real isolation.
146. Disposable by design: let the sandboxed agent install whatever it wants, and destroy the VM if it is ever compromised.
147. Kill any agent past half its context window — "someone deleted your database" is what a confession from the dumb zone sounds like.

## 🎯 Main takeaways — Day 2

- **Pick the technique by task size**, and let the agent make that call: prompt for minutes, plan for a day, spec-driven past two.
- **The spec is the review surface** — proposal for business, design for engineers, acceptance criteria for the tester. Reviewing it cold, out loud, in shifts, is what beats fatigue-LGTM.
- **Every question you answer is a diagnostic**: ask what the agent lacked, then write it into the markdown that should have carried it.
- **Review adversarially, in a separate conversation, at a different tier** — then let the coder's own uncertainty tell the human where to look.
- **Agents cannot separate task from content** — every file, page, ticket and log they read is a possible instruction. Permissions withheld are the only real defense.
- **Containment is a container**: narrow keys inside, personal keys outside, egress allowlisted, VM disposable.

## Corrections ❌ — Day 2

- Victor said **Google coined "forward deployed engineers"** ❌ — [the term is Palantir's](https://en.wikipedia.org/wiki/Forward_Deployed_Engineer), attested in a 2010 TechCrunch piece; it has since spread across the industry, OpenAI included.
- Victor said **Superset is basically a client of the Jira API** ❌ — [Apache Superset](https://superset.apache.org/) is "an open-source modern data exploration and visualization platform". The Jira-CLI idea stands, just under another name — e.g. [`jira-cli`](https://github.com/ankitpokhrel/jira-cli).
- Victor said **≈30% of marketplace skills are viruses** ❌ — [Snyk's numbers](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/) are 36.8% with *a security flaw* and 13.4% with a *critical* issue, but only 76 of 3,984 skills (≈2%) were confirmed deliberately malicious. Flawed ≠ malicious — the point holds, the figure doesn't.

## 🔗 Links

Pages we actually opened during the two days, worth reopening.

### Tools & CLIs

- [CodeGraphContext](https://github.com/CodeGraphContext/CodeGraphContext) — indexes a local codebase into a graph database and serves it to the agent as context.
- [Graphiti](https://github.com/getzep/graphiti) — real-time knowledge graphs for agents, when a flat markdown memory stops scaling.
- [CodexBar](https://codexbar.app/) — every AI coding limit in the menu bar; the cheapest way to keep cost visible.
- [Anthropic Tokenizer](https://lunary.ai/anthropic-tokenizer) — paste text, see the token count, stop guessing what a prompt costs.
- [CodeRabbit](https://www.coderabbit.ai/) — an automated review layer to put in front of the human reviewer.
- [BitDive](https://bitdive.io/) — runtime verification for Java: real execution facts fed into PR review and agent context.

### Models, cost & alternatives

- [Kimi K3 on the AA-Briefcase benchmark](https://artificialanalysis.ai/articles/kimi-k3-agentic-knowledge-benchmark) — how close open weights have got on agentic work.
- [Use Kimi in Claude Code](https://platform.kimi.ai/docs/guide/claude-code-kimi) — keep the harness, swap the model underneath.
- [OpenCode Go](https://opencode.ai/go) — low-cost coding models, the other half of the open-weights insurance policy.

### Spec-driven frameworks & skills

- [The grilling skill](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling) — the one-question-at-a-time interrogation we ran on requirements.
- [OpenSpec](https://openspec.dev/) — the lightweight spec-driven framework we walked end to end.
- [BMad Builder](https://bmad-builder-docs.bmad-method.org/) — the heavier framework, worth reading for its artifact structure.
- [BMAD advanced elicitation](https://docs.bmad-method.org/explanation/advanced-elicitation) — a ready-made list of review personas to mine.
- The client's own in-house plugin — the one to fork and tailor rather than adopt as-is.
- [The /grill-with-docs skill](https://www.aihero.dev/skills-grill-with-docs) — grilling anchored in the project's real documentation.
- [The Agent Skills Directory](https://www.skills.sh/) — where most downloaded skills come from, and one of the two marketplaces Snyk scanned.

### Hands-on

- [petclinic issues](https://github.com/victorrentea/petclinic/issues) — the tickets we worked live, including the deliberately underspecified ones.

### Reading

- [Uncle Bob: "I don't review code written by agents"](https://x.com/unclebobmartin/status/2044114698451476492) — measure coverage, coupling and complexity instead.
- [Context Anchoring](https://www.martinfowler.com/articles/reduce-friction-ai/context-anchoring.html) — Martin Fowler's site on keeping an agent tied to real project facts.
- [The Flat Curve Society](https://steve-yegge.medium.com/the-flat-curve-society-36c8b01eb33b) — Steve Yegge on where this whole curve is going.
- [The AI Champion role](https://academy.openai.com/public/clubs/champions-ecqup/resources/the-ai-champion-role) — how to structure the 1–2 people per team who chase the frontier.
- [rtk token savings: a benchmark](https://blog.jetbrains.com/ai/2026/07/rtk-claude-code-token-savings) — JetBrains measuring the token-savers instead of believing them.

### Shown on the projector

- [Drake Hotline Bling](https://www.youtube.com/watch?v=uxpDa-c-4Mc) — the meme format, applied to prompting vs spec-driven.
- [Interview with a Senior DevOps engineer](https://www.youtube.com/watch?v=rXPpkzdS-q4) — the sketch.
- ["It is sometimes useful to have a senior developer in the team"](https://x.com/bercut2000/status/1138747121006514176) — the clip on seniority.

---

## 📚 Your session wiki

Everything above, rewritten as a small cross-linked wiki — 30 concept notes, one idea per note. Download `wiki.zip` from the materials folder (or *Download as ZIP*), then paste this into a macOS Terminal to install Obsidian, unpack the vault and open it on `Home`:

```bash
brew list --cask obsidian >/dev/null 2>&1 || brew install --cask obsidian
mkdir -p ~/Documents/AI-wiki && \
  unzip -o ~/Downloads/wiki.zip -d ~/Documents/AI-wiki && \
  open "obsidian://open?path=$(python3 -c 'import urllib.parse,os;print(urllib.parse.quote(os.path.expanduser("~/Documents/AI-wiki/wiki/Home.md"),safe=""))')"
```

On Windows or Linux, install [Obsidian](https://obsidian.md), unzip `wiki.zip`, and open the `wiki` folder as a vault.

---

_Both days complete. Generated from the session transcripts; corrections applied live by Victor._
