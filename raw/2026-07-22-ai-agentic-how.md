# AI-Assisted Coding @ agentic.how — 2026-07-22

_generated in 5m 40s_

An open group of senior devs from several companies, most already deep in the tools — 70% said AI writes most of their code, and someone admitted losing sleep over Claude.
The day ran from tool hygiene and token economics in the morning, through grilling a vague ticket and spec-driven development after lunch, to adversarial code review and why the transformation has to start bottom-up.

## Tools, Models and Where the Room Stands

- Room poll: **70%** say AI already writes most of their code; [Simon Willison](https://simonwillison.net/) predicts **95% of the code** will be generated, by **the majority of engineers on the planet**.
- Worst possible combo = **Copilot IDE plugin in IntelliJ** — *"give it to someone you want to make hate AI"*; the CLIs (**Claude Code**, **Codex**, **Copilot CLI**) are the real tier.
- **No cheap models for coding** — no `-mini`, `-turbo`, **Haiku**: you have to maintain that code. Better to hand-write it.
- Effort dial: start `xhigh` for planning, drop to medium/low for pure code-spitting.
- The harness is not the brain: [Claude Code Router](https://github.com/musistudio/claude-code-router) runs CC's harness on GPT/DeepSeek/GLM — but API-key billing costs ≈10× a subscription.

## Hallucinations, the Dumb Zone and Context Hygiene

- Hallucinations show up in exactly two places:
  - context **>65–85% full** — the *dumb zone*
  - anything newer than the training data → [Context7](https://context7.com) (curated, up-to-date) beats WebFetch-ing some guy's blog ☢️
- **Never `/compact`** — you can't see what survived. Agent writes a handover markdown → *you read it* → `/clear` → resume. *"<mark>Good engineers are never auto-compacted</mark>."*
- Hand over at ≈50% context; needing it earlier means you mis-scoped the task.
- Sub-agents are the third escape hatch — but *warn*, don't micro-manage: "there's a legacy monster ahead, send minions" beats forcing a model choice, since each child re-pays the system prompt.

## Token Economics

- Where tokens actually die:
  - reading files you don't need → delegate to a sub-agent that reports compact findings ([research_codebase.md](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md))
  - tool definitions you never use → disable them (Victor saved **10k** killing Grafana + Atlassian), or enable [**ToolSearchTool**](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool)
  - verbose tool output (XML, `git status`, test logs) → [rtk](https://github.com/rtk-ai/rtk) or [headroom](https://github.com/headroomlabs-ai/headroom)
  - `AGENTS.md` over ≈200 lines
- Prefer a **CLI over an MCP** where one exists — every agent knows `gh`, its output is greppable, and it costs no tool definitions ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)).
- **XML is the worst thing you can put in a context window**, JSON nearly as bad — CSV halves it.
- **Prompt cache = a ≈5-minute window** on the prefix: saying "hi" to a 100k-token conversation cost **$20**, then $0.50 within 5 min, $20 again after an hour.
- So never leave a 300k-token agent idle while you review its code. 🤖 Anthropic also sells a [**1-hour cache TTL**](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) at higher write cost — worth it exactly for long human-review loops.
- **"Caveman" prompting is not worth it** — it compresses output toward the weakest link, you. Just put "keep explanations concise" in CLAUDE.md.

## CLAUDE.md, AGENTS.md and Skills

- Symlink `CLAUDE.md` ↔ `AGENTS.md`, keep it **on git**, bootstrap with `/init` — *"embarrassing to admit your repo has none"* ([AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md)).
- It holds **only the non-obvious**: delete the `mvn test` / `npm test` boilerplate every model already knows.
- **Anything that can drift, don't restate** — point at the generated OpenAPI YAML (guarded by a sync test) instead of listing endpoints ([OpenApiExtractorTest.java](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/OpenApiExtractorTest.java)).
- Add lines **anger-driven**: when the agent fails, ask *"what led you to believe that was right?"*, find the missing knowledge, write it down.
- Don't shout at the agent — criticism triggers **sycophancy**, not learning. *"Do not call their baby ugly"*; the best agentic engineers are managers and parents.
- Mine **past PR comments** via `gh` for rules newcomers needed, then sanitize with **Sonnet** (Opus over-classifies things as "obvious") — expect ≈10–20 keepers out of hundreds.
- Split by scope: per-subfolder CLAUDE.md, and promote conditional/procedural knowledge into **skills**; review monthly for drift.
- Skills load by **description only** (60–300 tokens, state the purpose twice) — a skill that never activates has a description too vague. [`paths:` frontmatter](https://code.claude.com/docs/en/skills) forces it to load before touching matching files.
- **Markdown for creativity, shell script for anything deterministic** — fewer tokens, less waiting, fewer hallucinations.
- Vet every skill you adopt: security ([**Snyk: ≈37%**](https://snyk.io/blog/toxicskills-malicious-ai-agent-skills-clawhub/) of public marketplace skills carry a security flaw), efficiency, and **mental erosion**. Tell the agent the *problem*, not the *steps*.

## Test Feedback Loops and Hooks

- A TDD-ing agent should run **only the fast, relevant unit tests**; anything >5s belongs in a hook, not the inner loop.
- Three places to force the slow tests:
  - a **[harness stop hook](https://code.claude.com/docs/en/hooks)** in `.claude/settings.json` ([settings.json](https://github.com/victorrentea/petclinic/blob/main/.claude/settings.json))
  - a [**git pre-push hook**](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-push)
  - CI — with the agent **watching** the build status and auto-fixing red ([watch-ci-after-push.sh](https://github.com/victorrentea/petclinic/blob/main/.claude/hooks/watch-ci-after-push.sh))
- A post-edit formatter hook **invalidates the agent's file cache**, forcing re-reads — run fast linters inline, slow ones (Sonar, 3 min) at pre-push.
- First things to spend agentic capacity on: speed up the build, kill flaky tests, raise coverage, finish old refactorings — repay debt before features.
- **Copilot CLI** ([`npm i -g @github/copilot`](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-cli)) rides a plain Copilot subscription; a company policy banning it is worth rioting against 😡.

## Grilling a Vague Ticket

- **"A Jira with very little info <mark>is a declaration of war.</mark>"** Two answers: grill the author, or reject any ticket not written with AI — *"you can't get too much 💩 past an AI."*
- Use the [grilling skill](https://github.com/mattpocock/skills/tree/main/skills/productivity/grilling): interview relentlessly, **one question at a time**, recommend an answer per question, look facts up instead of asking — the decisions stay yours.
- Ordering matters: the **pivotal question prunes whole branches** — "server-side or client-side?" makes most of the frontend questions moot.
- The group's own questions on "paginate the owners grid" (AI typically pulls 13–17):
  - server-side vs client-side — asked as *"how much data do we expect?"*, since analysts can't answer the technical form
  - default page size, default sort direction
  - sort by **first or last name** — AI silently picks last name "because that's what humans do"
  - which columns are nonsense to sort (pets, phone, address)
  - **state reset**: re-sorting while on page 2 — jump to page 1 or stay?
  - search/filter → rejected as **feature creep**, which Opus will happily suggest
- Hand-estimate for the same feature: 2–3 days — and you'd only *discover* the page-2 re-sort problem on day two.

## Vibe Horizon and the Vibe Fixer

- **Not reading code is an act of faith** — the whole vibe-coding bet in one line.
- **Vibe horizon ≈ 2–3 months.** After $3000/month of tokens Victor's project collapsed into:
  - race bugs, deadlocks, performance issues
  - a security glitch
  - drift — wrong API calls at scale
  - **vibe heresy**: dead ideas resurrecting out of leftover `.md` files
- **"<mark>Vibe fixer</mark>" = the SWE profession of tomorrow** — business hands you their vibe-coded dream, you make it scalable, secure and observable. Already a real job title.
- Devibing moves: introduce **DTOs** (his Python API passed raw dicts), break complex modules down, decouple — then **red-team it with AI**: *"can you still return a dict?"*
- Visualize the mess with **[CodeCity](https://wettel.github.io/codecity.html)** — 3D: area = file size, height = complexity, colour = commit heat.

## Speeding Up the Agent Loop

- **Vibe your developer tooling with no mercy** — it's the safest place to be reckless.
- Attack integration-test time with every hack available:
  - pre-seeded schema + dummy data at DB boot
  - heavy parallelization, one single Spring context enforced by a failing build
  - predictive test selection from call-graph analysis of the changed code
  - bytecode hot-swap over the debug port, `@Lazy`, [Spring Modulith](https://docs.spring.io/spring-modulith/reference/testing.html) single-module boot, [Azul CRaC](https://docs.azul.com/core/crac/crac-introduction)
- Then hand the list to an agent and **let it loop until the runtime drops** — 20s of Spring boot is the floor.
- **Java is the worst language for agentic coding** — slow startup plus verbosity burns tokens; Kotlin/Python/Go are compact.

## Stupid Questions Are Missing Context

- When the agent asks a **stupid question**, the answer belongs in **CLAUDE.md** — e.g. "we'll have 10,000 owners in production".
- *"You think it's stupid because you know more than the agent."* Reframe every dumb question as a context gap.
- It recommended sorting by phone and pets because it had **never seen a data sample** — give it a **DB MCP** ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)) on staging plus standing permission to query before reasoning.
- Push back on stupid business requirements ("sort by all columns") — with evidence, not opinion.
- Ego check: AI taught a 16-year Spring trainer about [`PagedModel`](https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html) — *"you will never be the smartest person in a room, even if you are alone."*

## Never Review an AI Proposal Alone

- **"Do not assume you know this shit. You don't."** — AI raised **[collation](https://www.postgresql.org/docs/current/collation.html)** in a sort spec and nobody in the room knew it.
- No brainstorm or markdown gets reviewed alone — ideally **mob-review** it, pulling in the DBA.
- When AI proposes a concept nobody understands, don't ask it to explain — ask for a **minimal scaffold with unit tests** you can run, and freeze the design until the team gets it.
- *"If a single word is not clear for the junior in the team, you unleash jihad."* — the anti-**skill-erosion** move.
- Save each such experiment as a knowledge pill in your own repo — [Simon Willison's TIL habit](https://til.simonwillison.net/).

## The Three Spec Documents

- **proposal.md is purely non-technical**: why, benefit, requirements, and **impact** ("what can I break?") — reviewed with the **PO**.
  - Add *"I don't know JSON"* to the grilling prompt to keep the whole conversation business-level.
  - Then invite PO + testers to a [Three Amigos](https://agilealliance.org/glossary/three-amigos/) session — afterwards, *"now let's talk dirty"* and raise technicality.
- **design.md is where your brain dies** — decisions explained one by one like an [ADR](https://adr.github.io/); reviewed by **QA** for critical thinking and acceptance criteria.
- **Non-goals are mandatory** in design.md — AI over-implements ("more tokens, why not?"), so consciously write what NOT to do.
- **tasks.md** is skimmed diagonally; with a strong model you barely need it. It's the **AFK phase** — ≈30 min, no keyboard, lots of tokens.
- 🤖 In [OpenSpec](https://github.com/Fission-AI/OpenSpec) (brownfield-oriented) the archived spec + acceptance criteria survive and accumulate into a reverse-engineered picture of the system; the rest is deleted.
- Pick the depth by size: **direct prompt** for a few manual hours · **plan mode / brainstorming** for a day or unknown territory · **brainstorm + spec-driven** for epics worth sleeping over.
- Precede any legacy work with **code research** — "if I did this myself I'd spend a day reading code" makes it send sub-agents instead of flooding your context.
- **"If your brain dies halfway in a markdown, stop. Take a shower, go to bed."**

## AI Calisthenics: Connect Everything

- Calisthenics, not lifestyle — exercises to stretch your limits:
  - type nothing by hand, only prompt; speak the prompts aloud
  - describe **what hurts**, never the solution (**"answer injection"**) — "boot this service with its dependencies", not "write a docker-compose"
  - run 3–4 parallel agent conversations, none idle >5 min
  - never paste text into the CLI — *"if you pasted it, connect that system instead"*
- Four ways to connect an agent to a system: **MCP** ([.mcp.json](https://github.com/victorrentea/petclinic/blob/main/.mcp.json)) · **CLI** (cheapest in tokens) · **API/curl** · **driving the browser** in your logged-in session (e.g. reading production Grafana).
- **"No one can stop you from connecting the agent to anything you can access."** Scariest steps last: inbox, a team bot, AI repairing production to cut MTTR.
- Cost warning: people have burned **$10k in a day** forcing Opus to brute-force read all the logs after it warned "boss, this is madness".
- Production data: read-only, preferably a **replica** or an anonymized mirror — or a **local** model that only sees the DB and emits data patterns for your test-data factory.

## Adversarial Reviewers and Easing Code Review

- **One conversation per concern**, born fresh with the right skills ([reviewer-reuse.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-reuse.md), [reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md)):
  - security · performance · design & extensibility · auditability/legal · UX consistency
- Never bolt security onto a dying 600k-token coding conversation — *"you want a brilliant baby pen tester"*, not an exhausted coder.
- Reviewers get the **same inputs as the coder** — ticket, acceptance criteria, deploy instructions — **plus the git diff**.
- The coder must not invoke its own reviewers: wrap it in a **hard-coded workflow** (or a stop hook / CI) that calls them; the coder is just a worker in a cubicle.
- Cheap entry point: **`/code-review`** in Claude Code or Copilot, customizable via `review.md`.
- Cross-vendor review for extra blood — *tell GPT the code was written by Opus*. Two independent LLMs missing the same slop is a probability argument, not faith.
- Visualize the diff instead of reading it: **DB schema**, package [PlantUML](https://plantuml.com/) ([packages.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/packages.puml)), domain model ([DomainModel.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/docs/generated/DomainModel.puml)) — red = new, with an [ArchUnit](https://www.archunit.org/) test [failing when code drifts from the diagram](https://www.archunit.org/userguide/html/000_Index.html#_plantuml_component_diagrams_as_rules).
- **Sequence diagrams generated from traces** at 100% sampling on the end-to-end tests ([add-visit.feature](https://github.com/victorrentea/petclinic/blob/main/petclinic-ui-test/features/add-visit.feature)), committed to git ([add-a-visit-to-an-existing-pet.puml](https://github.com/victorrentea/petclinic/blob/main/petclinic-ui-test/features/generated_sequences/add-a-visit-to-an-existing-pet.puml)) — that's how Victor found a scheduler-vs-websocket race condition.
- Gate with **Sonar/Fortify/PMD**, then merge main to prove the PR is mergeable — *before* a human is called.
- The killer prompt: *"Present your work descending by where you disagreed with the reviewer, ignored its findings, or weren't confident — with images. You have 30 minutes until my brain dies."*
- 🤖 Put critical files (OpenAPI, SQL, domain PlantUML) under [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) ([.github/CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS)) so touching them forces an elder's approval — two companies now formally review <50% of PRs.

## When Business Hands You Vibe-Coded Work

- Three escalating handovers into the Three Amigos meeting:
  - a shitty small Jira, and war in the meeting
  - a beautiful AI-refined Jira with acceptance criteria
  - a **working vibe-coded prototype** — devs called it *the best thing that ever happened*, zero room for misunderstanding
- Requirements still always land in Jira; you either brainstorm forward or reverse-engineer them from the vibed screens.
- Faced with 10,000 lines nobody read: **clean it up, don't regenerate**.
- Under ≈100k LOC a mess isn't worth an AI rewrite; [hexagonal](https://alistair.cockburn.us/hexagonal-architecture/) only pays once integration junk creeps into business logic.
- Never "Opus, rewrite everything" — take the **thinnest vertical slice**, review it, repeat; otherwise 3h in the dumb zone and an unreviewable diff.

## The Transformation Has to Be Bottom-Up

- Top-down fails when the C-suite wants the future but nobody signs *"I accept the business risk"* — *"it is dead on the table"*.
- A separate silo doing agentic workflows with no dev accountable for the production deployment is sick — FOMO, but it did buy you the subscriptions.
- Start small yourself: **OpenSpec, grilling, code review** — shipping 3× faster just exposes the next bottleneck the org never noticed (fuzzy requirements, slow build, missing tests, legacy).
- *"You cannot stop a developer from doing stuff. We are kings."* — anchored by the base64-split zip that walked a virus past a bank's antivirus.

## 🎯 Main takeaways

- **Never `/compact`** — hand over through a markdown *you* read at ≈50% context, before the **dumb zone** above 65–85%.
- **Every stupid question the agent asks is a missing line in `CLAUDE.md`** — add it anger-driven, and only if it's non-obvious.
- **A vague Jira is a declaration of war** — grill it one question at a time, pivotal question first, before any code.
- **Never review an AI proposal alone, and never let the coder review itself** — one fresh conversation per concern.
- **Not reading code is an act of faith** — the **vibe horizon is ≈2–3 months**, then you pay in races, drift and security.
- **If you pasted it, connect that system instead** — CLI before MCP, and vibe your own tooling with no mercy.
- **Start bottom-up, on yourself** — shipping 3× faster only exposes the next bottleneck the org never noticed.

<!-- SESSION-WIKI-START -->
---

## 📚 Session wiki

Everything above is also a small interconnected [Obsidian](https://obsidian.md) vault — one note per concept, heavy `[[links]]`, only what we covered today. Paste this in a macOS Terminal to install Obsidian (if missing), download the wiki and open it on `Home`:

```bash
# AI@agentic.how 2026-07-22 — fetch this session's wiki and open it on Home (macOS)
ZIP_URL="https://drive.google.com/uc?export=download&id=1A1hEUxXvAurGDJAP8I7rHWQF0tvZu1Vn"
DEST="$HOME/agentic-how-2026-07-22-wiki"
[ -d "/Applications/Obsidian.app" ] || { command -v brew >/dev/null && brew install --cask obsidian || open "https://obsidian.md/download"; }
mkdir -p "$DEST" && curl -L "$ZIP_URL" -o /tmp/session-wiki.zip && unzip -o /tmp/session-wiki.zip -d "$DEST"
python3 -c 'import json,os,sys,secrets,time; V=os.path.join(sys.argv[1],"wiki"); p=os.path.expanduser("~/Library/Application Support/obsidian/obsidian.json"); os.makedirs(os.path.dirname(p),exist_ok=True); d=json.load(open(p)) if os.path.exists(p) else {}; v=d.setdefault("vaults",{}); [x.pop("open",None) for x in v.values()]; k=next((kk for kk,x in v.items() if x.get("path")==V), secrets.token_hex(8)); v.setdefault(k,{"path":V,"ts":int(time.time()*1000)})["open"]=True; json.dump(d,open(p,"w"))' "$DEST" 2>/dev/null \
  && open "obsidian://open?path=$(python3 -c 'import urllib.parse,os,sys; print(urllib.parse.quote(os.path.join(sys.argv[1],"wiki","Home.md"),safe=""))' "$DEST")" \
  || open -a Obsidian "$DEST/wiki"
```

First launch asks whether you trust the vault → **Trust author and enable plugins**. Manual alternative: install [Obsidian](https://obsidian.md), *"Open folder as vault"* → pick `wiki/`, start from **Home**.
<!-- SESSION-WIKI-END -->
