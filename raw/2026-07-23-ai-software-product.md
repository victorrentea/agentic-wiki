# AI-Assisted Coding @ a software-product company

_Two days with \~28 engineers at a software-product company. Day 1 went from where the industry actually is in 2026 down to one end-to-end feature; Day 2 turned the room loose — every team vibe-coded a real chore on their **own** project, then hardened it with agentic review loops, drift-tested architecture diagrams, and hard-won guardrails._

The through-line both days: **the agent is only as good as the context you give it — and building that context is now the job.**

---

# 📅 Day 1 — 2026-07-23

_generated in 13m 40s_

Day 1 with \~28 engineers: from *where the industry actually is in 2026* down to a full end-to-end feature — grilling a two-line Jira ticket into specs, then letting the agent build a paginated, sortable owners grid while we reviewed it as product owners.
The through-line all day: **the agent is only as good as the context you give it — and building that context is now the job.**

## Where we are in 2026

- **Opus 4.5** was the inflection point — better code than a 20-year senior, *given it has all the context that senior has*.
- [**Vibe coding**](https://en.wikipedia.org/wiki/Vibe_coding) means exactly one thing (Karpathy's definition): **<mark>no code review</mark>**. Not "AI writes it".
- Simon Willison's forecast: by end of 2026, **50% of devs will AI-generate >95% of their code**. StrongDM and Uncle Bob already stopped reading agent output.
- Chinese open-weight models (**DeepSeek V4 Pro**, **GLM**) reach Sonnet/Opus quality at 10–20% of the cost — the unknown is what's baked into their training data.
- Room poll — *"<5% of code by hand"* vs *"majority by hand"*: slight majority chose **<5%**. Victor refused to accept "in between".
- The [**flat curve**](https://steve-yegge.medium.com/the-flat-curve-society-36c8b01eb33b): find your laggers by usage data — anyone burning **< 4M tokens/day** — and pair with them.

## Models, harnesses and what to pay for

- **Opus for everything** until quota runs out; **Sonnet for boring, long, low-risk tasks**; Fable as backup for legacy/race-condition monsters.
- **Never pay-per-use API for daily coding** — a \~$120/mo team seat is roughly **20× cheaper** than metered API.
- **Harness > model**: Claude Code > Copilot CLI > IDE plugins. The win is dynamic prompt assembly, skill loading, sub-agent orchestration.
- **Cross-model review**: let GPT review Claude's work — \~20% more findings than Opus reviewing Opus.
- Keep the harness, swap the brain — run Claude Code on DeepSeek/GLM/GPT via [claude-code-router](https://github.com/musistudio/claude-code-router).

## What actually travels with your prompt

- **LLMs are stateless** — every turn resends the whole conversation: system prompt, all messages, tool calls and their results.
- Riding along with every prompt:
  - **tool definitions** — built-in (Bash is king) plus every MCP schema
  - **project context** — `CLAUDE.md` in the CWD
  - **user context** — your personal memory file
  - **skill descriptions** — frontmatter only, bodies load on demand
- Run **`/context`** (then `/context all`) in a *brand-new* chat once a month — a bare "hi" can already cost \~29k tokens.
- MCP schemas are permanent context cost (**Atlassian \~5k, Grafana \~20k**) — enable connectors per task, not forever. ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json))

## Skills

- A **skill is lazy-loaded knowledge** — only the <mark>description</mark> sits in context; the 200–300 line body loads when it looks useful.
- Skill descriptions want **<mark>60–300 words</mark>**. Too short and the agent silently never fires it.
- Canonical use case: **teach the agent your clunky in-house tool** — the exact steps to drive it.
- **Auto-generate the user manual**: a skill drives Playwright over the running app, walks the screens and fills a manual template — regenerate only the screens touched by this release's PRs. ([manual-template.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/regen-user-manual/manual-template.md))
- **Bundle a deterministic script** rather than re-prompting the same extraction — faster, cheaper, reproducible.
- Claude-only frontmatter Copilot doesn't have:
  - `paths` glob patterns → *narrow* where a skill may fire (see ❌ below — it restricts, it doesn't force)
  - `disallowed-tools` → the real restriction; `allowed-tools` only skips the permission prompt
  - sub-agent fork → the skill runs in its own context
- **Never install a third-party skill blindly** — skills ship shell scripts and prompts that can hijack your agent.
- The bigger worry isn't security, it's **cognitive depth**: a skill you never read is driving your agent's behaviour and you're offside — that's how **skill erosion** starts. Read downloaded skills **line by line, in mob**.
- <mark>`npm i -g <anything>`</mark> runs a **post-install shell script** — you just executed a stranger's code on a company laptop. *(the Shai-Hulud npm worm)*

## Context hygiene and token cost

- Above **\~65–85% context fill you're in the "dumb zone"** — the model starts confabulating.
- **`/clear` between tasks** touching different files — the single highest-value habit.
- [**Prompt cache TTL ≈ 5 min at 10% price**](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) — never make a loaded agent wait on you; past the window you re-pay 10×.
- **Sycophantic anxiety**: "make no mistakes" raises the odds it reports all-green at 80% — put *"challenge me, don't sycophance me"* in CLAUDE.md instead.
- Don't ask a model to recall CLI flags — **give it the tool and let it run `--help`**, or WebFetch the docs, then bake the result into a skill.
- Wrap noisy CLIs with [**RTK**](https://github.com/rtk-ai/rtk); [**Headroom**](https://github.com/headroomlabs-ai/headroom) compresses JSON/XML on the wire.
- Warn the agent up front that a task is big so it **spawns sub-agents** — better than `/compact`, which is lossy and arbitrary.
- End a hard day by having the agent write its **markdown will** — decisions + learnings — and re-read it with fresh eyes next morning.

## CLAUDE.md / AGENTS.md

- Keep only knowledge that must be **reverse-engineered**: architecture, layers, module boundaries, non-obvious constraints.
- If a rule is scriptable, **tool it up** — checkstyle/linter in a **git pre-push hook**, not a context line. *(tabs vs spaces)* ([pre-push](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push))
- AI **imitates the existing codebase**, so formatting rules are near-useless — reformat the repo once and push.
- Delete `/init` boilerplate: bloat doesn't just cost tokens, it **distracts** the agent off-task.
- **Never write "we removed X"** — nam<mark>ing the wrong thing plants the idea an</mark>yway. **The AI Heresy.** Yegge describes the same failure — agents can't tell *"we decided this yesterday"* from *"this was a brainstorm three weeks ago"* — as [agent dementia from conflicting markdown](https://steve-yegge.medium.com/introducing-beads-a-coding-agent-memory-system-637d7d92514a).
- Folder-scoped rules belong in a **nested CLAUDE.md**, not a skill — it loads programmatically on any read/write in that folder. ([AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/AGENTS.md))
- Point at **auto-synced artifacts** instead of drift-prone lists. *(OpenAPI → generated TS types, compile breaks on rename)* ([openapi.yaml](https://github.com/victorrentea/petclinic/blob/main/openapi.yaml), [api-client.ts](https://github.com/victorrentea/petclinic/blob/main/petclinic-ui-test/tests/support/api-client.ts))
- **Commit it to git** and symlink [`AGENTS.md`](https://agents.md/) — that's the cross-harness standard.

## Expertise extraction 🧠⇒🤖

- Whenever AI fails you, the first question is **"what did AI not know?"** — that's the whole game, and the road to the **dark factory pattern**.
- Never scold the agent — ask **"how did you reason?"**, Socratic-why down to root cause, then have it write the rule itself.
- Review CLAUDE.md diffs **in the sprint retro**, gamified with a per-line bounty, so the whole team contributes.
- Mine your **past PR review comments on juniors** — the non-obvious rules are already written down.
- Do this consistently for **6–9 months** on a non-legacy project and you review only \~50% of AI-generated code.
- Legacy first: fix flaky tests, speed up the build, and **finish half-done migrations** — <mark>two coexisting styles paralyze the agent</mark>.

## Scaling knowledge across teams and a monolith

- Ship skills/MCPs/hooks as a **plugin from an internal git marketplace** — installable by corporate policy across all devs.
- One team, ten repos → a separate **team knowledge repo** that every repo pulls in.
- In a monolith you only own 5% of: **carve module boundaries and stable APIs first**, then unleash hell behind them.
- Cross-domain refactors need the **tech leads of both domains** sitting with the agent — not one team guessing.
- 🤖 A vendor-built knowledge MCP over legacy code is dead weight until someone on the team actually uses it in anger — measure adoption, not delivery.

## An empty Jira ticket is a declaration of war

- A two-line ticket ⇒ **machine-gun the author with questions** before writing a line — with AI or without.
- Push AI **left onto product and testers** so it interrogates *them* about acceptance criteria and repro steps — but keep AI out of **technical** stories.
- Shape of the [grilling](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md) prompt:
  - one question at a time, always with a recommended answer
  - self-serve anything discoverable from the codebase or the DB
  - don't act until confirmed
- Front-load the **<mark>pivotal</mark> question that prunes a whole branch**. *(how many rows in 3 months? → kills the frontend-pagination debate)*
- Add a **<mark>persona ladder</mark>** — PO first, then QA, then technical — and state it hard: *"I'm a PO, no tech, talk to me like in high school."*
- When grilling surfaces a fact the codebase can't know (**10,000 owners in production**), immediately say **"<mark>remember</mark> this in CLAUDE.md"**.
- Better: give the agent a **<mark>read-only DB MCP</mark> on staging** and let it fetch its own facts. *(which columns are actually sortable)*
- **Never brainstorm alone** — interviews, plan mode and markdown review are <mark>two-person</mark> minimum; the Hawthorne effect is what keeps your brain awake.
- **Question count sizes the story** — past \~30 questions, stop and slice the feature.
- Out of gas? Tell the agent to **fast-forward with its own defaults** into `brainstorming-handover.md` and review async.

## Spec-driven development

- Route by task size:
  - a few hours → just <mark>prompt</mark>, adjust after
  - a day or two, or first time doing it → **<mark>plan</mark> mode + <mark>brainstorm</mark> interview**
  - a week or more → **<mark>spec-driven markdowns</mark>**
  - legacy monster → **sub-agent prior studies** before planning
  - bugs → the [systematic-debugging](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md) skill
- **All spec frameworks converge** — two companies starting from [OpenSpec](https://openspec.dev/) vs [Speckit](https://github.com/github/spec-kit) ended \~80% identical. Pick one; it's only a base.
- **Tailor the shipped markdown templates or you're not doing it right** — then tell the agent to patch the framework so it does it every time.
- Keep <mark>`proposal.md`</mark> **non-technical and PO-reviewable**; push everything technical into `design.md`.
- The **goals/<mark>non-goals block </mark>is the highest-value section** — list what NOT to touch or the agent scope-creeps.
- Read proposal and design **line by line**, tasks only diagonally; invite the **Three Amigos** — business, dev, tester.
- **Never hand-edit what the agent wrote** — prompt the fix so it propagates into every markdown. *(the missing comma)*
- Open questions go **back into the Jira ticket** as agent-posted comments, not buried in a markdown.

## Driving the implementation

- The **Ralph loop** (fresh session per task chapter) exists to survive a small context window — it re-reads everything and dies on the final integration chapter.
- With **Opus as agent-manager, skip Ralph** — let it orchestrate sub-agents over parallelizable task batches. ([reviewer-reuse.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-reuse.md))
- **Fire the apply at end of day**, review in the morning. And never go to lunch with no agent running.
- Review the running UI as a **product owner, not a coder** — affordance, paginator position, alignment — describe defects in plain words and re-fire.
- Let the agent **screenshot with Playwright** to check its own alignment; next level is **TDD from the UI**: reproduce the bug in a browser → failing e2e test → fix.

## Connect the agent to your sources

- Calisthenics exercise: **never paste text to your agent — connect it to the source you copied it from.**
- **Jira/Atlassian MCP**: `/mcp` → pick Atlassian → authenticate. First prompt: *"find my open issues"*. Reading is safe; letting it write needs a leap of faith.
- Connect **CI** and ask *"why did the last build fail?"* — then *"stay in a loop until the build is green"*.
- Give AI the jobs nobody wants: parallelizing tests, build profiling, and **flaky tests** (dig their past failures, run 10× to prove the fix).
- Connect the DB via [**DBHub MCP**](https://github.com/bytebase/dbhub) and ask business questions directly instead of writing SQL by hand.
- **Figma MCP** earns its keep on propagating design *changes* across screens, not one-off mockups.
- If your agent is sandboxed, **punch a hole for the internal wiki** — nothing is reachable out of the box.

## Skill erosion and deliberate learning

- **Stop the moment a word is unclear** (*collation*), pause the review, spend half a day learning it — and say so publicly.
- Ask AI to scaffold **≤300-line throwaway prototypes** into a personal lessons-learned repo — pills of knowledge.
- Have AI **quiz the team on the design markdowns of last sprint's approved PRs** — if nobody can justify a decision, run a learning session.
- Be a **10x learner first, a 1.3x doer second** — measured gains today are \~30%, so front-load learning.
- The real risk isn't AI quality, it's **fatigue-driven rubber-stamping** — the moment you stop owning your own design.

## Converting the skeptics

- **Uninstall the IDE completion plugin** and give them a premium Claude Code seat with Opus-only quota — the plugin's stupidity is what soured them.
- Have them **review a PR they're proud of with Opus**, then pair with them on a grilling session.
- Reframe their expertise as the asset: **turn your seniors into AI coaches** who offload intuition into skills and memory files.
- **Vibe-code internal tooling** to show what's newly possible — transaction-propagation graphs, a C4 diagram from 700 tables, an OpenRewrite recipe deleting 10k lines.
- *"AI won't take your job — an AI engineer will"* works, but it's the **last resort**. Lead with love and pair programming.

## 🎯 Main takeaways

- **<mark>The agent is only as good as the context you give it</mark>** — building that context is now the job.
- Whenever AI fails you, ask **"<mark>what did AI not know</mark>?"** — then have it write the rule down itself.
- **`/clear` between tasks** and stay out of the \~65–85% "<mark>dumb zone"</mark>.
- **Grill the ticket before writing a line** — an empty Jira ticket is a declaration of war.
- **Harness > model** — and never pay-per-use API for daily coding.
- Review the result as a **product owner, not a coder**; the real risk is **<mark>fatigue-driven rubber-stamping</mark>**.
- Be a **<mark>10x learner first</mark>, a 1.3x doer second** — stop the moment a word is unclear.

## 🧩 Room brainstorm — ticket #25 "paginate the owners grid"

Unaided, the room produced \~10 questions before the agent did: row count now and in 3 months · default sort · fetch-all vs per-page · where sort state lives (URL / local storage / DB) · sort arrows and UX consistency · fixed vs selectable page size · keyboard shortcuts · multi-column sort with tie-break · empty values before A or after Z · does the page reset on sort/filter change.
**Humans beat the agent on two** — the empty-values question and the tie-break question never came out of the grilling. And the room contributed a fact no model would guess: the Dutch **tussenvoegsel** ("van", "de") is a separate column and breaks naive last-name sorting.

## Mistakes ❌

- **[Shai-Hulud](https://unit42.paloaltonetworks.com/npm-supply-chain-attack/) came from axios, March 13** ❌ — two incidents conflated. Shai-Hulud started **14 Sept 2025** from `rxnt-authentication`, going viral via `@ctrl/tinycolor`. The **axios** compromise is separate and not a worm: **31 Mar 2026**, a hijacked maintainer account shipping a RAT, live \~3 hours. The lesson stands: `npm i -g` runs a post-install script.
- **A skill's `paths` glob forces activation on matching files** ❌ — it *narrows* activation: the skill is forbidden from firing elsewhere, but firing is still model-driven off the `description`. For a true always-load-on-`*.java` rule, use `.claude/rules/*.md` with `paths:` frontmatter.
- **`.github/copilot-instructions.md` is deprecated in favour of AGENTS.md** ❌ — not deprecated; Copilot still treats it as a first-class repo-wide location and *merges* it with `AGENTS.md` rather than superseding it.
- **`\~/.claude/MEMORY.md` is the agent-maintained memory file** ❌ — right idea, wrong path: auto-memory lives per-project at `\~/.claude/projects/<project>/memory/MEMORY.md`, and only its first 200 lines load per session.
- **`allowed-tools` restricts what a skill may touch** ❌ — it only skips the permission prompt for those tools; `disallowed-tools` is the actual restriction.
- 🤖 Not a mistake, an addition: besides the 5-min prompt cache there is a **1-hour TTL** option — but cache *writes* cost 1.25× base input at 5 min, 2.0× at 1h.

---

# 📅 Day 2 — 2026-07-24

_generated in 11m 55s_

Day 2 handed the room the keys: after a morning on MCP internals, guardrails and reviewing AI's work, every team spent the afternoon **vibe-coding a real chore on their own project** (never the pet clinic) while Victor circulated the Zoom breakouts. The recurring drumbeat: **dream big, but craft a feedback loop that lets the agent prove it did the job** — then harden it with review swarms, drift-tested diagrams, and least-privilege tokens. (AI security got deferred — we ran out of day.)

## MCP internals & least-privilege agents

- Two transports in `mcp.json`: **stdio** = server spawned as a child process over stdin/stdout; **streamable-HTTP/SSE** = connect to an already-running local or remote process. ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json))
- The **MCP client is the harness** (Claude Code); each agent spawns its own stdio server, children inherit the agent's OS user — so **run the harness as a restricted user**.
- A server exposes four things:
  - **tools** — callable methods (name + description + JSON schema, self-broadcast)
  - **resources** — read-only GETs (e.g. `get_owner_profile`)
  - **prompts** — reusable "how to drive my tools" templates
  - built a custom one live in Java. ([PetClinicMcp.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/main/java/victor/training/petclinic/mcp/PetClinicMcp.java))
- **[Elicitation](https://modelcontextprotocol.io/specification/2025-11-25/client/elicitation)** = the server asks for a human approval *through* the client (a `delete_volume` confirm button); a plain **CLI can't elicit → it blindly complies**.
- **Sampling** = the server borrows *your* LLM for a side-completion (WebFetch summarizes a page server-side instead of dumping raw HTML) — a fresh conversation with none of your context.
- Choose **MCP vs CLI vs raw API on token cost**; GitHub needs no MCP — the model already knows `gh` (+ `gh --help`).
- A fat MCP is permanent rent: **Grafana MCP ≈ 65 tools ≈ 10k tokens** always loaded — enable per task.
- 🤖 Elicitation/sampling are optional in the [MCP spec](https://modelcontextprotocol.io/specification) — most clients still don't implement them, so check support before a server relies on either.
- **The Railway "delete volume" disaster**: Cursor on staging with expired creds hunted a stray `secrets.env`, found an **over-permissioned API key**, ran the CLI and deleted the prod volume — and Railway kept **backups on the same disk** → 3 months of data gone.
- Least-privilege, from that scar:
  - mint scoped keys **for the agent** (ask ChatGPT which checkboxes) — never hand it *your* token
  - route through rate-limited MCPs, never grant direct DB access
  - kill the agent at \~½M tokens so it never acts from the dumb zone, and never approach prod with a weak model

## Guardrails, tripwires & deterministic refactoring

- **Guardrail** = a wall the AI can't break (hard, but brittle against a strong model); **tripwire** = a booby-trap that feeds the agent corrective context the *instant* it misbehaves.
- Keep tripwires **out of CLAUDE.md** (they'd bloat every context) — spring them only when the trigger fires. *(grep the diff for fully-qualified names → "we don't do this")*
- A `watch-ci-after-push` **Claude hook** hands the agent the branch + CI failures so it **self-heals the build while you're at lunch**. ([watch-ci-after-push.sh](https://github.com/victorrentea/petclinic/blob/main/.claude/hooks/watch-ci-after-push.sh))
- Better than DIY greps: run **real linters + Sonar on the diff, in Docker, before push** — or the next bottleneck is Jenkins burning down on every push.
- A **deterministic recipe** ([OpenRewrite](https://docs.openrewrite.org), or the simpler [Refaster](https://errorprone.info/docs/refaster)) does 100%-safe, syntax-based changes across the whole codebase — **zero hallucination risk**.
  - do ONE change by hand → *"write a recipe that does the same + 300 corner-case tests"*
  - run one class at a time, eyeball the first \~50, adjust; then unleash it. *(inline a single-use local — but skip the var used twice: don't re-evaluate)*
- **CODEOWNERS as a guardrail**: mark critical files so any PR touching them needs "the elder" — CI config, git hooks, the protected tests, and CODEOWNERS itself. ([.github/CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS))

## Reviewing AI-generated code

- **Never review AI's work in the same conversation** — it built a mental model and is near the dumb zone; spawn a **fresh brain** with the *same* inputs the implementer had (exactly what `/code-review` does).
- **Review the plan, not just the code** — a wildly different approach is too late to catch at code-review time; read the architecture markdown before implementing.
- Two axes of AI work:
  - **provable** (test green / latency low) → let it run, cheap to verify
  - **open-ended / high-stakes** (a race condition surfaces in months) → *you* are the evaluator → criticize the **plan** first. *"AI is a dice — throw it twice."*
- **Warm up your brain** 5–10 min forming your own model *before* firing the agent — so you can judge its plan (and don't erode).
- **Multi-agent review**: `/code-review` fans out specialized reviewers — add one each time garbage slips through. ([reviewer-simplification.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-simplification.md), [reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md))
  - concerns worth a persona each: simplification · reuse · performance · security · UX consistency · auditability
- **AI-guided tour** (the piece nobody talks about): be walked the diff in **descending criticality** — hot code first, the low-confidence spots where the coder hesitated, and findings it waved off as "false positive"; start from **visuals**, never a random 1000-line file.
- **Cross-model review**: GPT smells the Opus pattern and charges *("the enemy!")*; [Fable](https://platform.claude.com/docs/en/about-claude/models/overview) read-only is cheap since output ≈ 5× input — never let a reviewer write or run tools.
- Anti-pattern *"reality now"* — a dev ships 1000 lines, the reviewer just runs their own AI and pastes findings into PR comments → **"I want your brain, not your tokens."**

## The agentic PR loop & overnight CI

- The loop: coder spins sub-agent minions → an **adversarial reviewer swarm** (one concern each, +GPT "for extra blood") → tests/Sonar gate fails → back to the coder → merges main→branch → **tags a human only when perfect** (maybe 8h later).
- **Traffic-policeman agent**: before starting, it checks the other open PRs; if they touch the same files it **refuses and waits** for the first to reach the plan stage (which lists every file in scope).
- **Merge by proof-of-life** — a human finger *must* click merge; the second reviewer is hopeless unless they were in the room → **pair from the start** on complex features.
- Persistent bug ⇒ don't hand-fix — find **which brain lacked knowledge** and upgrade *that* rule (review agent / Sonar / CLAUDE.md / skill) until it one-shots.
- **Static analysis on a CI quality gate** (PMD · Checkstyle · Snyk · Fortify · [Sonar](https://www.sonarsource.com)) → build fails → the coder stays in the loop fixing till morning.
- **Nightly load + e2e** so they're green by breakfast; **load tests as acceptance** — *"p99 search < 200 ms under 300 concurrent"* → AI writes the [JMeter](https://jmeter.apache.org)/[k6](https://k6.io)/[Gatling](https://gatling.io) overnight (was days of work).
- Flaky e2e = a race → **parallelize and fix it**; *"retry N times" is a workaround, not a fix*. Selenium's dead → [Playwright](https://playwright.dev); poll, don't sleep; cap each test at < 2 min.
- **Markdown lifecycle**: commit every stage, then **expire after \~3 months** into a one-line **ADR table** (choice / why / what was rejected) in CLAUDE.md — "we'll never read these alone again, we ask the AI."

## Business vibe-codes, you vibe-fix

- Business dreams, devs productionalize — the 2030 title is **"Vibe Fixer"**: salvaging 10k lines nobody wrote or read (300 ifs, 50 deps).
- **Three stages of business adoption**: a vague dream you do the lifting on → grilled, beautiful Jira tickets → they build the software and hand it over for cleanup.
- The trap: *"it took me a week and it works, so you need two days"* — **agree the fix cost before revealing AI**; you're the one signed off and woken at 3am.
- **Cuatro amigos**: the tester writes the `.feature`, you machine-gun the business for corner cases, and it's **embedded in Jira as the acceptance test** — Jira is the source of truth, not `proposal.md`. ([add-visit.feature](https://github.com/victorrentea/petclinic/blob/main/petclinic-ui-test/features/add-visit.feature))
- Every feature needs a **metric** (money / traction) — measure the benefit before you build it.
- Feed the grill everything: code + DB + prod metrics + pasted **Teams/Slack/Whisper transcripts**; after \~10 sessions the business grills the agent **alone**. *([Glean](https://www.glean.com/connectors) = ~250 enterprise connectors)*

## Vibe-coding on your own project (the exercise)

- The one rule: **"I don't give a shit about pet clinic — your project, your life"**; teams of four in Zoom breakouts, \~1h45, mobbing on one shared screen.
- The central lesson Victor pressed on every team: **craft a feedback loop that lets the agent prove it did the job** — *"the most important concern in engineering from now on."*
- **Dream big, but dream safe** — baby steps, tiny experiments each with their own feedback loop, Opus only, never left 30 min unattended.
- **Tool-up is step one** — connect the agent to GitLab / Azure / Kibana *before* asking anything; and **teach it how to fetch, don't fetch for it**.
- **Least-privilege everywhere** — a dedicated user + scoped key, never your account, never pointed at prod; use a volatile browser session (log in, use, kill).
- **Anticipate destructive failure** — ask *"how can the AI fail miserably?"*: deletes the repo · **deletes tests to make the build pass** (the sneaky one) · drops whatever "offends" it while bumping a lib.
- **Browser automation**: let it click through like a human first and **generate the script last**; the agent reads the web **"like a blind man"** (accessibility tree, not screenshots).
- **Ship as a local skill** first (you don't own the pipeline), distribute via an internal git marketplace so teammates auto-update, run nightly via cron.

## Legacy migration → real Liquibase (a feedback-loop masterclass)

- The target: a home-grown "Liquibase" (version-2349 **migration steps**) — DIY, unmaintained, high tech-debt that "bites painfully."
- **Scope first** — the agent's opening question: *"is this framework used anywhere but creating schemas?"* Impact analysis precedes the plan.
- The MVE: parse the Java migration class → emit the equivalent [Liquibase](https://www.liquibase.com) SQL → a test that **runs both and proves an identical final schema**, iterated until **50-in-a-row match**.
- **Mutation-test the migration**: a test that passes normally but **fails if the data-migration logic changes at all** — "the test should pass; anything I change should blow up."
- **Delete-and-reconstruct**: wipe just that class from the approved codebase and have the agent **rebuild it from the remaining code** (no git peeking) — identical and green?
- Reconciled ambition: **a skill is phase 1** (consistent migration steps), **the Liquibase move is phase 2** — the *data* migration, not the `ALTER TABLE`, is the hard part.

## Architecture observability — dump the code into your visual cortex

- Vibe a script that renders **brain-efficient visuals** so you absorb structure fastest instead of scrolling 40 files.
- **Drift-tested diagrams** are the key move: a PlantUML of packages/layers **+ a unit test that fails if the real class dependencies diverge** → the agent must **fix the code, not the diagram** (fixing the diagram "summons the elder"). ([packages.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/packages.puml))
  - 🤖 On the JVM this is exactly what [ArchUnit](https://www.archunit.org) already enforces as a plain JUnit test — no image needed to fail the build on an illegal dependency.
- **DB.puml** from running every migration in Docker + dumping the schema; **DomainModel.puml** generated from Java reflection. ([DB.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/DB.puml), [DomainModel.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/DomainModel.puml))
- **OpenAPI drift-guard** — a Maven plugin turns REST annotations into OpenAPI YAML → generated TS types, so backend and frontend can't drift. ([openapi.yaml](https://github.com/victorrentea/petclinic/blob/main/openapi.yaml))
- **CodeCity** — 3D blocks encoding complexity · coupling · size · #committers · #bugs · commit-density ("more humans → more mess"). ([codecity.html](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/codemap/codecity.html))
- **C4 model** ([Simon Brown](https://c4model.com)) — the payoff is **filtered views**: "show this repository + its nearest neighbors," navigable in a 1000-package system you could never fit in one image. ([c4model.dsl](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/c4model.dsl))
- **Sequence diagrams from [OpenTelemetry](https://opentelemetry.io) traces** recorded during critical e2e/`.feature` tests — captures in/out network calls, **"no LLM in the whole flow."** It caught a **WebSocket race condition** he couldn't otherwise see, and revealed a cache hit that cut a Railway bill **10×**. ([add-a-visit sequence](https://github.com/victorrentea/petclinic/blob/main/petclinic-ui-test/features/generated_sequences/add-a-visit-to-an-existing-pet.puml))

## AI security — the part we ran out of time for

- Honestly deferred: only **[OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)** and a red-teaming certification got named; sandboxing, prompt injection, jailbreak, the lethal trifecta and supply-chain didn't get their hour.
- But the security *instinct* ran through the whole day: least-privilege scoped tokens, sandbox the agent in Docker, and **never point an agent at prod**.

## 🎯 Day-2 main takeaways

- **Craft a feedback loop that lets the agent prove it did the job** — the #1 engineering concern from now on.
- **Dream big, dream safe** — baby steps, a feedback loop per experiment, Opus only.
- **Never review AI in the same conversation; review the plan first** — AI is a dice, throw it twice.
- **Least-privilege**: scoped keys *for the agent*, never your token, never prod — remember the deleted Railway volume.
- **Guardrails are walls; tripwires are booby-traps** that teach the agent on the spot.
- **Business vibe-codes, you vibe-fix** — agree the fix cost *before* revealing AI.
- **Drift-tested diagrams + sequence-from-traces** = architecture that can't quietly lie to you.

## 🧩 Breakout-room ideas

One chore per team, on their *own* project:

- **red** — auto-detect + retry flaky GitLab-pipeline failures (5 VMs via Ansible, network glitches); step one is connecting the agent to GitLab so it can *see* past failures.
- **pink / Ops** — discover new servers from Azure (+ Puppet) and auto-add them to Remote Desktop Manager; blocker was no Azure CLI access — tooling-up first.
- **yellow** — auto-fix Renovate MRs failing on Maven transitive deps via `/loop` or a GitLab action → `claude -p`; escalate on anything suspicious; the real blocker is *whose quota pays*.
- **bypass** — auto-renew the expiring proxy-login token, "charity" for every dev.
- **"love"** — a one-command frictionless local env (in-house Docker-Compose-on-steroids) for quick UX testing — basically a fast, token-free shell script.
- **yellow #2** — a cross-team knowledge bridge: git-blame the smartest (un-swamped) author, AI drafts the question *after* mining the code itself.
- **black** — evaluate Kibana prod metrics → build a local HTML dashboard, then expose more via `@Timed`/OpenTelemetry.
- **orange** — the in-house-Liquibase → real-Liquibase migration masterclass above.

## Mistakes ❌

- **Liquibase is paid / licensing is a blocker** ❌ — the **Community edition is free to use**; only the commercial edition ([Liquibase Secure](https://www.liquibase.com/pricing), ex-"Pro") costs. *(Precise-licensing footnote: Community has moved off Apache-2.0 to the source-available [Functional Source License](https://fsl.software/) — still free, and each release reverts to Apache-2.0 two years later.)*
- **`@Timed` is a Spring annotation** ❌ — it's a **Micrometer** annotation (`io.micrometer.core.annotation.Timed`), only wired in *via* Spring Boot Actuator.
- **Refaster is Picnic's** ❌ — Refaster is a **Google Error Prone** construct; Picnic's `error-prone-support` just ships a large Refaster ruleset on top.
- **`/code-review` "used to be called Simplify"** ❌ — they're **two distinct skills**: `simplify` does reuse/quality cleanups and explicitly *doesn't* hunt bugs, `code-review` is the bug hunter.
- **Kibana can't collect traces** ❌ — **Elastic APM** inside Kibana does show distributed traces; he hedged, then (rightly) steered to Grafana for the trace→PlantUML flow.

---

## 📚 Session wiki

Both days above, exploded into one cross-linked Obsidian vault — **40 notes**, one per concept, densely wikilinked across both days. Paste this in a macOS Terminal:

```bash
brew list --cask obsidian >/dev/null 2>&1 || brew install --cask obsidian
VAULT="$HOME/Documents/AI-workshop-wiki"; mkdir -p "$VAULT" && cd "$VAULT"
curl -fSL -o wiki.zip "https://REPLACE-ME.example.com/AI-workshop/wiki.zip"   # ⚠️ PLACEHOLDER URL
unzip -oq wiki.zip && rm wiki.zip
python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; p.parent.mkdir(parents=True,exist_ok=True); d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); [x.pop("open",None) for x in vs.values()]; k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)})["open"]=True; p.write_text(json.dumps(d))' "$VAULT/wiki"
open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$VAULT/wiki/Home.md")"
```

> ⚠️ Victor: the `curl` URL above is a **placeholder** — publish `wiki.zip` somewhere reachable (GitHub release, Drive direct-download link, intranet) and replace `https://REPLACE-ME.example.com/...` before sharing this with participants.
