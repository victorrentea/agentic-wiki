# Agentic Engineering @ JAX London — 2026-10-09

_generated in 10m 53s_

A one-day hands-on workshop at JAX London with an international group of Java, TypeScript and Elixir developers.
The arc: we packaged team knowledge as agents, skills, plugins and hooks, then made the agent safe with sandboxes, then took a real petclinic feature from grill-me to OpenSpec to a polished UI.

## Adoption & cost

**A [$39 Copilot seat](https://docs.github.com/en/copilot/get-started/plans) is $2 a day — engineers need $100+, or a personal subscription.**

- **Never use Copilot inside IntelliJ** — Microsoft's plugin, JetBrains can't fix it.
- **Subscriptions are subsidized**: 2×[$100 plans](https://claude.com/pricing) burn ≈$8-12k a month at API prices. ⚓ *$8k/month startup pair*

## Vibe coding

![Slide 7 — AI in SWE: History](https://lh3.googleusercontent.com/d/1RQWkUbz6R_yUo8OXMQYfLv5dESBDif6L)

**[Vibe coding](https://simonwillison.net/2025/Mar/19/vibe-coding/) means not knowing the technical solution the AI built — not merely letting AI type.**

- [Dark factory](https://simonwillison.net/2026/Feb/7/software-factory/) trick: **a hidden holdout set of acceptance tests** the coding agent can't game.
- Uncle Bob **stopped reviewing code**, watching [CRAP metrics](https://www.artima.com/weblogs/viewpost.jsp?thread=210575) instead.
  - Victor still reviews: Uncle Bob's are sandbox projects, not legacy monsters.

## Custom agents

**A custom agent is just a markdown persona in [.claude/agents](https://code.claude.com/docs/en/sub-agents#choose-the-subagent-scope) — so never say you developed an agent.**

- [reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/jax26/.claude/agents/reviewer-efficiency.md): **one concern only**, Opus, no web search. ⚓ *ignores 100-line methods*
- Start it with **[`claude --agent reviewer-efficiency`](https://code.claude.com/docs/en/sub-agents#invoke-subagents-explicitly)**, or ask a running chat to spawn it as subagent.
- **Harness = body**, LLM = brain. ⚓ *interview word*

## Models & effort

![Slide 9](https://lh3.googleusercontent.com/d/1CdzDCXnK9dI4lX9KUXy6RMHFvgsPvk1h)

**Default to Opus on high and keep guru models for planning and critique, never coding.**

- **>150 seats or HIPAA means [API pricing](https://claude.com/pricing)** — only then plan with Opus, implement with Sonnet.
  - Otherwise token prices only fall: Opus or above, even on pet projects.
- Guru models **plan monumental changes and review legacy refactors**. ⚓ *18 h, $1,000 OpenGL port*
  - Never grunt work ⚓ *an hour of Fable only spawning subagents*
- **[Effort](https://code.claude.com/docs/en/model-config#choose-an-effort-level): high by default**, xhigh across system boundaries, never max.
- **Over 1 h idle, the [prompt cache](https://code.claude.com/docs/en/prompt-caching#cache-lifetime) expires**. ⚓ *"hi" = $5, quota 63→69%*
  - Learn to estimate tasks in dollars: over $50 is a red flag.
- **Local models only for test-provable tasks** — subtle consistency bugs slip past them.
  - Air-gapped defense: plan with Opus at home, implement locally — borderline leak.

## Instruction files

**Make the agent contradict you, skip what you know, and compact auto-learned rules every week.**

- **"Challenge my prompts"** in [AGENTS.md](https://github.com/victorrentea/petclinic/blob/jax26/AGENTS.md) kills sycophancy. ⚓ *be a Russian with me*
- **Prefer BDD-level end-to-end/API tests** over mock-heavy unit tests: best safety per review minute. ⚓ *[human-review](https://github.com/victorrentea/human-review) demos: [visit has vet](https://victorrentea.github.io/human-review/demo/review.html), [owners grid](https://victorrentea.github.io/human-review/demo2/review.html)*
- Home [CLAUDE.md](https://code.claude.com/docs/en/memory): **"I know X, teach me Y"** turns the agent into a tutor.
  - `be brief` ≈ [caveman](https://github.com/JuliusBrussee/caveman) ≈ [output style concise](https://code.claude.com/docs/en/output-styles#concise): ≈30% fewer words, same code.
- **"Update docs on every change" explodes AGENTS.md** to 3,000 lines in weeks. ⚓ *auto-learning loop*
  - Fix: a Sunday 10 PM cron burns leftover weekly quota reconciling and compacting.

## Guardrails

**Don't plead in markdown for what a linter can enforce — burn it into hooks, CI and CODEOWNERS.**

- **Add a rule only after the AI breaks it** — no model writes 120-char lines unprompted.
  - Unless your code does: garbage in, garbage out ⚓ *Copilot SQL injection on EJB 2.1*
- **Layer the enforcement**, each catching what the previous one misses:
  - [pre-push Git hook](https://git-scm.com/docs/githooks#_pre_push) runs the linter, but [`--no-verify`](https://git-scm.com/docs/git-push) skips it
  - [CI build](https://github.com/victorrentea/petclinic/blob/jax26/.github/workflows/ci.yml) re-runs the checks server-side
  - [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/jax26/.github/CODEOWNERS) forces elder review on workflow edits ⚓ *meet your creator*
  - 🤖 CODEOWNERS blocks merges only with branch protection's ["Require review from Code Owners"](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) on.
- **Review markdown diffs first** in every sprint retrospective.

## Skills

**A skill lazy-loads rules from AGENTS.md, firing only when its front-matter description matches the task.**

- **Front matter** = the [YAML header](https://code.claude.com/docs/en/skills#frontmatter-reference) atop [SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/java-code-style/SKILL.md). ⚓ *interview question*
- **≈60-70k tokens before "hi"**: tool descriptions, both instruction files, [skill descriptions](https://code.claude.com/docs/en/skills#skill-descriptions-are-cut-short).
- **Too short a description and the skill [never fires](https://code.claude.com/docs/en/skills#skill-not-triggering)** — aim ≈60 tokens, say the trigger twice.
- Plain markdown links are **[progressive disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)** too: read on demand.

## Plugin marketplaces

**Share team skills across repos through an internal-Git marketplace with a feedback skill for PRs back.**

- **[Internal Git](https://code.claude.com/docs/en/plugins/host-marketplace#grant-access-to-a-private-marketplace) only** — a public marketplace downloads and runs strangers' markdown.
- [Plugins](https://code.claude.com/docs/en/plugins) bundle skills, MCPs, agents, hooks, and **[auto-update](https://code.claude.com/docs/en/plugins/install#keep-plugins-updated)** from colleagues' pushes.
- **First skill to add: `feedback`** — no way to PR back, Victor won't install it.
- **Two marketplaces**: company infra (Jira, MCPs), plus the team's way of working.
- Demo: **[java-code-style plugin](https://github.com/victorrentea/petclinic/blob/jax26/plugin-marketplace-demo/plugins/java-code-style/skills/java-code-style/SKILL.md)**.

## Hooks

**Extend a skill you don't own via a hook that injects extra markdown, not a rival skill.**

- **Skill activation is a [tool call](https://code.claude.com/docs/en/skills)**, so a [PostToolUse hook](https://code.claude.com/docs/en/hooks#posttooluse-decision-control) can append markdown.
- Demo: **inject `java-code-style-extension.md`**. ⚓ *blank line after every signature*

## Browser & E2E video

**Every bug fix ships with an AI-recorded video reproducing it before and after.**

- QA rule: **reproduce bugs hands-off the mouse**, talking while AI clicks.
  - [AI-written ticket](https://github.com/victorrentea/petclinic/issues/40) tells: periods ending bullets, arrows, bold, emojis
- Prompt: bug fix plus **[Playwright video](https://playwright.dev/docs/videos) before/after** with synthetic voice. ⚓ *[OpenAI harness engineering](https://openai.com/index/harness-engineering/)*
- **No MCP needed**: a [browser-cli skill](https://github.com/victorrentea/petclinic/blob/jax26/.claude/skills/browser-cli/SKILL.md) drives [Playwright CLI](https://github.com/microsoft/playwright-cli) from the terminal.

## Subagents

**Long task lists go to an Opus orchestrator of subagents, not a Ralph-loop script.**

- [Ralph loop](https://ghuntley.com/ralph/): [`claude -p`](https://code.claude.com/docs/en/headless) in a bash `for`, fresh context per task, **dies at final integration**.
- Multi-day runs pay off with **a measurable goal**. ⚓ *weekend build speed-up, flaky tests*

## Auto mode

**Let auto mode's second LLM approve bash: human approvals decay and regex denies leak.**

- **[Bash deny patterns](https://code.claude.com/docs/en/permissions#bash-rule-limits) are best-effort**: `git commit -q --no-verify` slips past.
  - New: [soft/hard deny rules in prose](https://code.claude.com/docs/en/auto-mode-config#override-the-block-and-allow-rules), e.g. "never send repo contents out"
- **[Autopilot](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/autopilot) is not [auto mode](https://code.claude.com/docs/en/permission-modes#eliminate-prompts-with-auto-mode)**: it never asks you, it forces assumptions.

## Sandboxing

![Slide 77 — OS-harness /sandbox](https://lh3.googleusercontent.com/d/134_gBVI0wYu797BKMF7II6JWH6DszyVk)

**Sandbox the agent and give it fake keys, swapped for real ones only at egress.**

- [`/sandbox`](https://code.claude.com/docs/en/sandboxing) = OS sandbox: **allowed hosts and folders**, inherited by every child process.
- Against **`.env` exfiltration**: [a proxy swaps fake keys](https://code.claude.com/docs/en/sandboxing#mask-credentials) for real ones, headers only, expected host only.

## MicroVM

**Run sensitive agents in a microVM: own kernel, own Docker daemon, agent-only credentials.**

- Plain Docker: **no Docker-in-Docker** without escape-grade privileges, plus a shared kernel. ⚓ *[OpenAI's accidental attack on Hugging Face](https://simonwillison.net/2026/Jul/22/openai-cyberattack)*
- All-powerful legacy keys stay on the host behind **an [MCP](https://modelcontextprotocol.io/docs/getting-started/intro) exposing 2 tools, not 45**.
  - Jenkins, Jira, Confluence keys can't be scoped like GitHub fine-grained tokens
- Try **[Docker Sandboxes](https://docs.docker.com/ai/sandboxes/)** (`sbx`, [product page](https://www.docker.com/products/docker-sandboxes)), a ready [microVM](https://code.claude.com/docs/en/sandbox-environments#virtual-machine) with [credential swap](https://docs.docker.com/ai/sandboxes/architecture/).

## Production

![Slide 72 — AI Slop / Ops](https://lh3.googleusercontent.com/d/1yrIPMGQrg40zv-DxR9yzYja32M7tybt4)

**Approach production only with the strongest model, a sandbox, tiny tools, and an adversarial watcher.**

- **Never start an agent in `~`**: it reaches [`.ssh`](https://code.claude.com/docs/en/sandboxing#protect-credentials) and every forgotten secret. ⚓ *[agent wiped a startup's prod DB](https://x.com/lifeofjer/status/2048103471019434248)*
- Grow its powers slowly: **one more key per week**, via [MCP](https://modelcontextprotocol.io/docs/getting-started/intro), audited by a second agent.

## Context window

![Slide 17](https://lh3.googleusercontent.com/d/18izZ-Y--7JZmV-uNIbYqB9QpeQUctxg1)

**Every prompt [resends the whole conversation](https://code.claude.com/docs/en/prompt-caching#how-the-cache-is-organized), so a well-scoped task should never need compaction.**

- **Escape the dumb zone**:
  - [compact](https://code.claude.com/docs/en/prompt-caching#compacting-the-conversation) at a stability point, never mid-expansion
  - for marathons, delegate everything to [subagents](https://code.claude.com/docs/en/costs#delegate-verbose-operations-to-subagents)
  - handover markdown, then [/clear](https://code.claude.com/docs/en/costs#manage-context-proactively)
- **Compaction is a code smell**: task too big, or the agent wasn't warned.
  - 🤖 Re-inject rules lost at compaction via a [SessionStart hook](https://code.claude.com/docs/en/hooks), matcher compact

## Token saving

![Slide 19 — Token Saving](https://lh3.googleusercontent.com/d/1Y3whKusDoifL1mlH5-q-r_PMitIIZTGz)

**Keep the [system prompt lean](https://code.claude.com/docs/en/costs#move-instructions-from-claudemd-to-skills): every line is paid every turn.**

- **Catch, don't forbid**: fake "push failed: line >120" only when violated.

## Grill me

**Warm up your own brain first, then let grill-me interview you one question at a time.**

- Spend **5 minutes listing questions yourself** before meeting Opus.
  - why the feature, and which metric proves impact?
  - sort by phone, address, pets? Push back, say no
- Unknown territory: **[plan mode](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode) at minimum** (≈3 questions), grill-me for ≈23.
- [grill-me](https://github.com/mattpocock/skills): **top skill on [skills.sh](https://www.skills.sh/)**. ⚓ *around 2% of 1.25M skills malicious*
- First question, always: **paginate in browser or on server?**
  - 50k rows ⇒ server, but ask who pages through 100 pages
- **Teach, don't just answer**: 50k owners/year goes into `Volumetry.md`, linked from [AGENTS.md](https://github.com/victorrentea/petclinic/blob/jax26/AGENTS.md).

## Cloud agents

**Run long agent work in the cloud and keep local agents only for fast loops and running the app.**

- Give the agent its own [AgentMail](https://www.agentmail.to) inbox: **the email thread is its context**. ⚓ *Flux, eight round-trips*
- **[Claude Code cloud sessions](https://code.claude.com/docs/en/claude-code-on-the-web)** run [Docker](https://code.claude.com/docs/en/cloud-environments#installed-tools) and E2E tests with the laptop shut. ⚓ *3.5h build mid-flight*
- Parallel agents coordinate through **commit messages**, like open-source contributors, not chat.
  - [Worktrees](https://git-scm.com/docs/git-worktree) only for shared files or port clashes.

## Lunch debates

**Expose a strict schema via MCP or REST and let clients' agents map their concepts onto it.**

- Going **headless, [MCP](https://modelcontextprotocol.io/)-only** bets every customer drives an agent: risky for less AI-literate users.
  - Agent-driven clients cost tokens on every test run.
  - Yet a UI over a good MCP is vibe-coded in an hour. ([PetClinicMcp.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java))
- **Who owns the mapping** is a strategic call: *fecha de embarcación* → due date.
  - Theirs: ship the schema plus a skill so their agent scripts the transform.
  - Yours: one mapping folder per customer, times 4,000.
  - Either way, validate at the gate: [ISO dates](https://en.wikipedia.org/wiki/ISO_8601), invariants.
  - 🤖 DDD calls this an [anti-corruption layer](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer), client-side here.
- **Retirement interviews**: a month of AI-scripted sessions distils tacit knowledge into an [LLM wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f). ⚓ *Dutch bank, photos as cues*

## Real data

**Feed the agent what it cannot fetch: the business's numbers and the data you see.**

- Agents read the minimum, so **make it query real data via the DB CLI**. ⚓ *only name, city sortable*
- Unknown concept mid-grill? **Never fast-forward** → *Instruction files*. ⚓ *collation: one SQL guru*
  - 🤖 Sort with a locale-aware collation (DB [`COLLATE`](https://www.postgresql.org/docs/current/collation.html), Java [`Collator`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/text/Collator.html)), never byte order.

## Breaking changes

**Before a breaking change, learn who calls you — telemetry is the reality of the field.**

- Three ways to **find your consumers**:
  - [telemetry traces](https://opentelemetry.io/docs/concepts/signals/traces/), or trace IDs printed in logs
  - service-account permissions to call you
  - scream test, safe only with [flags](https://martinfowler.com/articles/feature-toggles.html) or [blue-green](https://martinfowler.com/bliki/BlueGreenDeployment.html)
- Keep a **draw.io diagram tested against traces** — an unexpected edge fails the build.
- Banks: **[shadow deployment](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/shadow-testing/)** — same input to old and new, outputs compared, writes suppressed.

## Spec-driven

**Start from any spec framework — OpenSpec, [Spec Kit](https://github.com/github/spec-kit), [Superpowers](https://github.com/obra/superpowers) — but never stay on its defaults.**

- [OpenSpec](https://github.com/Fission-AI/OpenSpec) ([openspec.dev](https://openspec.dev/)) [`openspec init`](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md) writes skills plus commands — **delete the commands**, skills cover both.
- **"Unchanged in six months? No raise."** Tailor to your team, skills, business.

## Task triage

![Slide 44 — Prompt vs Complexity](https://lh3.googleusercontent.com/d/1VXrLGqzHyF6jRlMurxYjQyRpk9EzAMAJ)

**Match technique to task size: do it, warn of legacy, grill the unknown, spec the multi-day.**

- Hours-sized task: **just do it**, Jira-to-PR without opening the IDE.
- Massive legacy: **warn, don't command** — "people died in there" lets it choose [explore subagents](https://code.claude.com/docs/en/sub-agents).
- Unknown territory: **a second terminal teaching you**. ⚓ *10-minute [Debezium](https://debezium.io/) prototype*

## Spec artifacts

**Keep the proposal readable by a product owner — technical details belong in design.md.**

- Ask it to move tech to design.md **and update the OpenSpec skill** accordingly. ⚓ *"who's JSON?"*
  - plus an ASCII UI sketch as the business's visual anchor
- Review with the [Three Amigos](https://www.agilealliance.org/glossary/three-amigos/): **QA brings the attacks**. ⚓ *[prompt injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/) in a logged address*
- [`/openspec-apply-change`](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md): **≈$15 and 20–45 min** of agent work.
  - tune it: parallel subagents on independent tracks

## Headless Claude

**Don't build a chatbot harness — `claude -p` with `--resume` already is one.**

- One web message = **one [`claude -p`](https://code.claude.com/docs/en/headless) turn**, [resumed by session ID](https://code.claude.com/docs/en/headless#continue-conversations).

## Knowledge sources

![Slide 57 — Agentic Workflow](https://lh3.googleusercontent.com/d/1FkX4QWhmdALvw6DU5H5JC72q_v8LO6Ng)

**Pasting text into the agent is illegal — connect every knowledge source via MCP or CLI.**

- **Sources** to wire in:
  - [Jira, Confluence](https://github.com/atlassian/atlassian-mcp-server), Slack/Teams, meeting transcripts
  - databases, [Grafana metrics](https://github.com/grafana/mcp-grafana), logs
- **No [MCP](https://modelcontextprotocol.io/docs/getting-started/intro) allowed? A CLI** with a personal access token, or a headless browser. ⚓ *shadow tooling at 3 banks*

## Business PRs

**The business will grill agents and vibe-code PRs itself — share your skills so their code arrives good.**

- **Agentification** levels:
  - 1: business grills an agent wired to your code
  - 2: business [vibe-codes](https://en.wikipedia.org/wiki/Vibe_coding), from "start app" to "add pagination"
- A **10k-line PR "from Business with Love"** — options:
  - accept as a prototype, input to brainstorming
  - remove business Git permissions
  - vibe-fix it, Victor's job in five years
- **[Skills](https://code.claude.com/docs/en/skills) live in Git for everyone**, business included — then most PRs get accepted.

## Break ideas

**Spend AI on filtering noise and classifying input, not on guessing business decisions.**

- A **YouTube-transcript skill** [deep-linking](https://support.google.com/youtube/answer/57741) the seconds worth watching beats watching. ⚓ *video is 4× slower*
- Win AI-denying engineers by **pairing and showing what they can build**, not by arguing. ⚓ *[NASA human computers](https://www.nasa.gov/history/when-the-computer-wore-a-skirt-langleys-computers-1935-1970/)*

## Visual feedback

**Polish UI live with users, pointing at screenshots instead of describing positions in words.**

- The room's nitpicks, **[dictated with a screenshot](https://code.claude.com/docs/en/common-workflows#work-with-images)**, landed in seconds:
  - sort arrows always visible: grey, bright white when active
  - no search button: [debounce](https://developer.mozilla.org/en-US/docs/Glossary/Debounce) the keystrokes instead
  - last name as [ghost text](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Attributes/placeholder) in a wider input
- "AI did it wrong" often means **nobody read the markdown carefully**. ⚓ *two-column sort never asked*
- **Circle it** on the screenshot. ⚓ *an earlier client workshop*

## Shift left

_Same flow as Slide 57 in Knowledge sources._

**Force agentification upstream: business and testers arrive with AI-drafted material before coding starts.**

- Seen once: a **[Jira hook](https://confluence.atlassian.com/adminjiraserver/advanced-workflow-configuration-938847443.html) rejecting tickets not written by AI**.
- Before the [three amigos](https://www.agilealliance.org/glossary/three-amigos/), send each attendee **seven bullets** extracted from the markdowns.

## Second-LLM review

**Let a second LLM review before you do, and turn every finding into a markdown fix.**

- Trace slop to its source and **edit that markdown** ([AGENTS.md](https://github.com/victorrentea/petclinic/blob/jax26/AGENTS.md)):
  - a careless upfront discussion
  - a requirements glitch nobody saw
  - an agent that needs brain surgery ([reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/jax26/.claude/agents/reviewer-efficiency.md))
- [Dark-factory](https://simonwillison.net/2026/Feb/7/software-factory/) rule: after **10 consecutive good PRs, review only half**. Any bad one resets the counter.
- **The biggest danger: approving what you didn't understand.**

## MCP

**MCP's edge over a CLI: the server calls back into your session mid-call.**

- **[stdio](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports) servers are booted by the client**: 12 Claude Code sessions, 12 copies. ⚓ *[`npx` in .mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)*
  - [Streamable HTTP](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports#streamable-http) servers run once, shared, e.g. [Context7](https://context7.com)
- JWT scopes **hide or show whole tools**, never single fields.
- **Three callbacks** a CLI can't do:
  - [elicitation](https://modelcontextprotocol.io/specification/2025-06-18/client/elicitation): "sure you want to drop the disk?" ([`call_vet_ambulance`](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java#L208))
  - [sampling](https://modelcontextprotocol.io/specification/2025-06-18/client/sampling): the server borrows your LLM to summarize a fetched page
  - [OAuth](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization) in the browser: transient tokens, no API keys

## LLM wiki

**Keep what you know apart from what your agent needs: one wiki per bounded context.**

- Two clients' wikis meet only through **a glossary, a context map**; merged, words lose meaning. ⚓ *[bounded context](https://martinfowler.com/bliki/BoundedContext.html)*
- Idea: **patterns repeat into principles, then values**, decaying when unused. ⚓ *OpenClaw "REM sleep"*

## Skill pipelines

**Let scripts call the LLM, not an LLM wrap your scripts: it derails less.**

- **State goals, not a roadmap**: a 3,000-line skill became 200, half the time, ⅔ the tokens.

## Corrections ❌

- Copilot caches prompts 45–60 min — Claude models measured at 5 min, the [API default](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).
- "A company called Dark Factory" ❌ — [StrongDM](https://factory.strongdm.ai/); dark factory is [the pattern](https://www.danshapiro.com/blog/2026/01/the-five-levels-from-spicy-autocomplete-to-the-software-factory/).
- A [fork bomb](https://en.wikipedia.org/wiki/Fork_bomb) only kills itself ❌ — it can freeze the whole machine ("crashing the system due to resource starvation").
- NAT = "Network Access Table" ❌ — [Network Address Translation](https://www.rfc-editor.org/rfc/rfc2663).
- LSE replays trades "since 1950" ❌ — [electronic only since 1986](https://en.wikipedia.org/wiki/Big_Bang_(financial_markets)).
- "Forward deployed engineer" is Google's ❌ — [Palantir's](https://en.wikipedia.org/wiki/Forward_deployed_engineer), in use by 2009.

## 🎯 Main takeaways

- **Vibe coding means not knowing the technical solution the AI built — not merely letting AI type.**
- **Don't plead in markdown for what a linter can enforce — burn it into hooks, CI and CODEOWNERS.**
- **Sandbox the agent and give it fake keys, swapped for real ones only at egress.**
- **Warm up your own brain first, then let grill-me interview you one question at a time.**
- **Feed the agent what it cannot fetch: the business's numbers and the data you see.**
- **Match technique to task size: do it, warn of legacy, grill the unknown, spec the multi-day.**
- **Let a second LLM review before you do, and turn every finding into a markdown fix.**

## 🔗 Links

**Testing & review**
- [human-review](https://github.com/victorrentea/human-review) — Victor's review-page skill; its demos lead with BDD-level acceptance tests
  - demos: [visit has vet](https://victorrentea.github.io/human-review/demo/review.html) · [owners grid, paged and sorted](https://victorrentea.github.io/human-review/demo2/review.html)
- [Bug #40: visit date has no range validation](https://github.com/victorrentea/petclinic/issues/40) — the AI-written ticket, fixed live with a before/after video

**Sandboxing & production safety**
- [Fork bomb](https://en.wikipedia.org/wiki/Fork_bomb) — the bash one-liner shown just before sandboxing
- [OpenAI's accidental cyberattack against Hugging Face](https://simonwillison.net/2026/Jul/22/openai-cyberattack) — boxed agents escape by digging into a shared kernel
- [Qwen3.8-27B-Uncensored](https://huggingface.co/orcarouter/Qwen3.8-27B-Uncensored-FP8) — an uncensored model to red-team your own sandbox; Opus won't help break out
- [Docker Sandboxes](https://www.docker.com/products/docker-sandboxes) — the `sbx` microVM Victor uses for sensitive work
- [An AI agent just destroyed our production data](https://x.com/lifeofjer/status/2048103471019434248) — over-privileged forgotten key; prod, staging and backups on one disk

**Claude Code**
- [Hooks reference](https://code.claude.com/docs/en/hooks) — yes, a hook can react to compaction
- [skills.sh](https://www.skills.sh/) — 1.25M skills, ≈2% malicious; grill-me tops it

**Spec-driven**
- [OpenSpec](https://openspec.dev/) — the spec framework installed live, then trimmed to skills only

**Agent memory (lunch debate)**
- [Mnemosyne](https://mnemosyne.site/) — a memory layer for Hermes Agent, for outgrowing a flat memory.md
- [Sentience](https://sentience.com/) — a participant's pick: assistants that remember you and act proactively
- [Introducing dots](https://openai.com/index/introducing-dots) — OpenAI's take on the same idea, the frontier lab moving in on such startups

**Industry & news**
- [SaaSocalypse](https://saasocalypse.com/) — SaaS struggles once agents can rebuild it; Victor cloned Teachable in a morning
- [Hacker News](https://news.ycombinator.com/) — a participant's news source; Victor prefers AlphaSignal's one line per story

## 📚 Session wiki

The session materials include `wiki.zip` — a small [Obsidian](https://obsidian.md) vault with **71 short, cross-linked notes**, one per idea discussed (custom agents, skills, plugin marketplaces, hooks, context window, sandboxing, grill-me, spec-driven development, the agentic workflow map, second-LLM review…), with the discussed slides inside. Browse it online at [interact.victorrentea.ro/3n27yu/wiki-site](https://interact.victorrentea.ro/3n27yu/wiki-site/), or on macOS paste this into a Terminal — it installs Obsidian if missing, downloads the wiki into `~/Documents/jax-agentic-wiki` and opens it on **Home**:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
D=~/Documents/jax-agentic-wiki; V="$D/wiki"; Z=$(mktemp -t wiki).zip
curl -fsSL -o "$Z" "https://drive.google.com/uc?export=download&id=1qBgv1ERn-Hii8M4MmpGTDZX4KmY6WBDX" && rm -rf "$D" && mkdir -p "$D" && unzip -qo "$Z" -d "$D" && python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)}); p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V" && open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")" || echo "Download failed: take wiki.zip from the session materials folder"
```

Start from **Home** and follow the `[[links]]` — it isn't meant to be read linearly. On Windows/Linux: unzip `wiki.zip` anywhere and open the `wiki` folder as a vault in Obsidian ("Open folder as vault").
