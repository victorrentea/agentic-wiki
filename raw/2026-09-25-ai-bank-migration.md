# Agentic Coding @ a bank — 2026-09-25

_generated in 7m 51s_

A hybrid day, room plus remote, with a bank's team — midway through moving EJB2 + Struts/JSP onto Spring Boot Modulith + Thymeleaf.
The arc: spend AI credits wisely → give the agent context, tools and guardrails → make it plan the migration with you before it codes.

## Client context

**On legacy, the team's knowledge is the asset — "we just have to validate" is why dark factories fail.**

- Target: **EJB2 + Struts/JSP → [Spring Boot Modulith](https://spring.io/projects/spring-modulith) + [Thymeleaf](https://www.thymeleaf.org/)**, Oracle untouched.
- **7 core screens** already live ⚓ *the cake*
- **Study the history first:** a previous rewrite by another team failed.
- **Legacy constraints:**
  - 45 external parties, 100+ foreign API connections
  - runs only from Eclipse on Tomcat 5.5
  - ≈15 years on the decom list
- Use the SDLC hub for **research**: Confluence + PBIs → markdown, not code.

## AI credits

**Credits are a daily flat white — and a home subscription can fund tools for work.**

- **10,000 credits = $100/month** = one flat white/day.
  - Room poll: ≈80% type under half their code by hand
- A personal subscription buys **≈60× its price** in [API-equivalent tokens](https://platform.claude.com/docs/en/about-claude/pricing) ⚓ *$1,800 in one week*
- **Build open-source tools at home**, [clone them at work](https://github.com/victorrentea/petclinic).
  - Opus-built spike = blueprint Copilot imitates well
  - Room pushback: org security must vet such tools
- Credits burn on searching: **point it at the files** involved.
- A **specific prompt** prevents costly repent-corrections.
- M365: project zip on SharePoint gets **top-tier GPT on a separate quota**.

## Prompt-driven typing

**Use Copilot chat in VS Code, never IntelliJ — ghost text is the free fallback.**

- **Copilot plugin in IntelliJ** makes a company hate AI ⚓ *"boss, help me"*
  - [Copilot CLI](https://docs.github.com/en/copilot/concepts/agents/about-copilot-cli), the best harness, is not granted yet
- [Comment](https://docs.github.com/en/copilot/concepts/completions/code-suggestions) + Enter + wait = **prompt-driven typing**, [zero credits](https://docs.github.com/en/copilot/concepts/billing/copilot-requests).
- Weakest model, narrow view: **a longer comment** rescued it ⚓ *non-Java linked list*

## OpenRewrite

**Code is a syntax tree, so AST-aware tools declutter legacy as safely as IDE refactorings.**

- [OpenRewrite](https://docs.openrewrite.org) became famous for **[Spring Boot 2 → 3](https://docs.openrewrite.org/running-recipes/popular-recipe-guides/migrate-to-spring-3)**.
- **Custom recipes:** [InlineSingleUseLocalVariable](https://github.com/victorrentea/petclinic/blob/main/refactoring-legacy/src/main/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariable.java) ⚓ *2 + 3 × 4 as a tree*
  - 🤖 run [rewrite:dryRun](https://docs.openrewrite.org/reference/rewrite-maven-plugin) and review the diff before applying
- Hand-written recipe: a week. **Copilot wrote it in 10 minutes.**
- Iterate on **counterexamples** first ⚓ *variable used twice* ([InlineSingleUseLocalVariableTest.java](https://github.com/victorrentea/petclinic/blob/main/refactoring-legacy/src/test/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariableTest.java))

## Model choice

**The wrong model tier either writes slop or wastes money — pick per task.**

- Untested legacy code: **anything below Sol is suicide** ⚓ *1.5–3M-LOC codebases*
  - cheaper tiers suffice for read-only research
- **[Auto mode](https://docs.github.com/copilot/concepts/auto-model-selection)** routes on your first prompt: a vague opener gets the intern.
  - fix: make it sound scary, or let M365 draft it
- **[Output costs](https://docs.github.com/en/copilot/reference/copilot-billing/models-and-pricing) ≈5× input**, so reviewing with Sol is cheap.
- **[Three tiers](https://github.blog/changelog/2026-07-09-openais-gpt-5-6-sol-terra-and-luna-are-now-available-in-github-copilot/):**
  - Sol, 40-year architect: original, long-horizon work
  - Terra, you: idiomatic planning, complex coding proven by tests
  - Luna, intern: simple tasks, logs
- **[Ask mode](https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/)** only near quota end: it makes you the AI's waiter.
- Long migrations: **[Sol 1M](https://developers.openai.com/api/docs/models/gpt-5.6-sol) at medium effort**, plus a quota guard.
- Self-hosted open models trade **≈a year of capability** for sovereignty ⚓ *[Kimi](https://github.com/MoonshotAI) flailed 40 minutes*

## Reviewer agent

**Review with a stronger sub-agent that reports findings — never let it auto-fix.**

- "…and fix any findings" makes the parent **blindly obey**. ([reviewer-efficiency.md](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md))
- The reviewer must **outrank the author** ⚓ *100k-line Sol PR, [Astra](https://github.blog/changelog/2026-09-04-gpt-6-astra-is-generally-available-in-github-copilot/) review*

## Token estimates

**Estimate a task in human hours plus token dollars.**

- **"4 hours, but $100"** is a valid estimate now.
- A typical day of 2–3 tasks costs **≈$20–30 in [tokens](https://docs.github.com/copilot/concepts/billing/usage-based-billing-for-individuals)**.

## Calisthenics

![Slide 111 — AI Calisthenics](https://lh3.googleusercontent.com/d/1y_07t1KdBFhWsUjD0vCO5yWyVaadhkc-)

**Drill like calisthenics: never type code, only prompt.**

- **Describe the problem**, not the fix ⚓ *"Father, I have sinned"*
- **Give an [example](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#use-examples-effectively)** instead of babysitting.

## Parallel agents

**A subagent whose only job is falsifying the first catches hallucinations.**

- Research prompt: "prove it with links; **adversarial sub-agents** challenge every link" ⚓ *"You lied to Victor!"*
  - token-cheap variant: "I will check your links"
- Check compliance citations with a **falsifying [subagent](https://code.visualstudio.com/docs/copilot/agents/subagents)** against a regulations DB ⚓ *ANAF's invented laws*
- **One concern per [reviewer](https://github.com/victorrentea/petclinic/blob/main/.claude/agents/reviewer-efficiency.md)**: twenty in one prompt dilute attention.
  - price: each agent re-reads the same files

## Hidden context

**AGENTS.md loads into every chat: house rules, never one PBI's details.**

- **≈40k words** reach the model before your first prompt ⚓ *red bicycle*
- Name the odd stack (EJB2, Struts, XML) or every chat **rediscovers it** ⚓ *ignored startup cache*
- PBI details: extract to markdown, **kill the chat**, restart from the file.
- [Path-scoped instructions](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions) inject **on file touch**, unlike skills that may misfire ⚓ *[pom.instructions.md](https://github.com/victorrentea/petclinic/blob/main/.github/instructions/pom.instructions.md)*
- Give the URL, never **paste a web page**: [hidden text](https://genai.owasp.org/llmrisk/llm01-prompt-injection/) rides along.
  - images too: white-on-white text in a CV
- **[Doc gardening](https://openai.com/index/harness-engineering/)**: a weekly cron agent folds the week's changes into the wiki.

## Deterministic diagrams

**Feed agents diagrams that scripts generate or tests verify, never LLM-drawn ones.**

- **Script the truth**, then point [AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md) at the files:
  - Maven module graph from the [dependency tree](https://maven.apache.org/plugins/maven-dependency-plugin/tree-mojo.html)
  - package diagram from Java packages
  - [openapi.yaml](https://github.com/victorrentea/petclinic/blob/main/openapi.yaml) dumped by a test
  - DB schema dumped from the CI test database
- Keep visual memory: hand-lay a **draw.io diagram**, then unit-test its edges.
- Feed **metadata, not pixels**: [draw.io PNGs embed their XML](https://www.drawio.com/doc/faq/export-to-png).
  - Gliffy: export the JSON source instead
- Cross-check drawn integrations against **[Kong's authorized callers](https://developer.konghq.com/plugins/acl/)**.
- Call hierarchy **regenerated by script**, no LLM ⚓ *OwnerRestController diagram*
- One query per util method reveals which **entry point** uses it.
  - tool: [CodeGraphContext](https://github.com/CodeGraphContext/CodeGraphContext)

## Guardrails

**Let tests, CI and CODEOWNERS police the agent, not your approvals.**

- [ArchUnit](https://www.archunit.org/) checks code against a **PlantUML diagram**: [PackagesArchTest](https://github.com/victorrentea/petclinic/blob/main/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/PackagesArchTest.java), [docs](https://www.archunit.org/userguide/html/000_Index.html#_plantuml_component_diagrams_as_rules).
- Agent touches the guardrail folder: [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS) forces **[review by the elders](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)**.
  - or it edits the diagram: a new green line in review ⚓ *architecture surveillance*
- AGENTS.md: "after every push, **make sure CI passes**" ⚓ *fixes Sonar while you lunch*
  - local [SonarQube for IDE](https://docs.sonarsource.com/sonarqube-for-vs-code/connect-your-ide/connected-mode) lacks the server ruleset
- Approving commands one by one is theatre: **approval fatigue** blinds even bash experts.
  - set Copilot permissions to [Autopilot](https://code.visualstudio.com/docs/agents/run/approvals)
- On a test DB, let the agent **hide the password** from itself ⚓ *SQLcl saved connection*

## CLI tools

**Wired to your CLIs, the agent reads Azure and Confluence for you.**

- [`az` CLI](https://learn.microsoft.com/en-us/cli/azure/boards): PBIs plus why the **[last build failed](https://learn.microsoft.com/en-us/cli/azure/pipelines/build)**.
- [Confluence CLI skill](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/confluence-cli/confluence.py): REST plus **[personal token](https://confluence.atlassian.com/enterprise/using-personal-access-tokens-1026032365.html)**, 10 minutes.
- It acts in your name: it drafts, **you publish**.
- Ask it to pass the **dependency check**, never "make the build green".
- Wrap the DB in a **shell-script skill** ([db-cli.sh](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/petclinic-db-cli/db-cli.sh)): one prompt, one Excel pie chart.
- [SQLcl](https://www.oracle.com/database/sqldeveloper/technologies/sqlcl/) beats raw JDBC: the **vendor CLI** knows all of Oracle.
  - a DDL dump in git greps faster than live schema queries
- Ask for **a script**, not an opinion ⚓ *400 tables, zero mistakes*
- Agent + **anonymized prod-like schema** tunes SQL via [explain plans](https://docs.oracle.com/en/database/oracle/oracle-database/23/sqlrf/EXPLAIN-PLAN.html) ⚓ *3-page query, seniors went white*
- Agent on real data: **fence it in** first.
  - replica, never production
  - read-only DB user
  - anonymized data, no internet
- Production: the agent writes the script, you read and **run it**.

## QA automation

**Never re-test by hand: narrate the test once and let a browser agent automate it.**

- Transcript + test case + [browser-cli](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/browser-cli/SKILL.md) → agent: you **review the [Playwright](https://playwright.dev/)**, never write it.
- Obvious bugs need no human: [ticket](https://github.com/victorrentea/petclinic/issues/40) → **failing test** → fix ⚓ *never lunch without an agent working*
  - az pulls the PBI, the browser reproduces it, a [Gherkin test](https://github.com/victorrentea/petclinic/blob/main/petclinic-test/src/visit-date-range.feature) fails, then passes

## Rule #1

**Every time the agent wrongs you, add one line to a markdown.**

- Don't slap it, **become its trainer** ([AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md)).
- The team's [AGENTS.md](https://agents.md/) is ≈150 lines: it needs **many more skills**.

## Team skills

**End a working session by distilling it into a scripted skill.**

- Prompt: "extract what you learned into a [db-cli](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/petclinic-db-cli/SKILL.md) skill, scripting **as much as you can**".
  - verify in a fresh session, without naming tables
  - Python beats Java: portable, no JVM startup
- A skill is **[front matter](https://code.visualstudio.com/docs/copilot/customization/agent-skills)** saying when to trigger, plus docs and a script ([confluence-cli](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/confluence-cli/SKILL.md)).
- Build a **generic connector**, not a page-bound skill ⚓ *Confluence skill for one page*
- Script inside the skill folder, token in an **uncommitted `.env`**.
- Only descriptions preload — **bodies load lazily**: [progressive disclosure](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills).
  - 50 skills: descriptions alone crowd the context
- [External marketplaces](https://code.visualstudio.com/docs/copilot/customization/agent-plugins): **auto-update** runs a stranger's code.
  - eval a [public skill](https://skills.sh) with vs. without before adopting it
- **Two layers** of internal skill repos:
  - org-wide: tool wrappers like browser, SQLcl, Confluence
  - per team: conventions like Java code style

## Clarify first

**Grill the requirement in business words, then persist the answers.**

- Ask **why** first: the answer was "it's slow".
  - it decides [paginating](https://github.com/victorrentea/petclinic/issues/25) in SQL versus the frontend
- The room's sorting questions nobody had **asked the business**:
  - tiebreaker on equal values?
  - default sort? given or family name?
  - never by address, phone or pets
- Sort the full set, **then cut**.
- In a bank, ask **"how many"** before "how fast".
  - persist "100k owners, patient bankers" as [AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md) volumetry
- [grill-me](https://github.com/mattpocock/skills) for multi-day tasks: **one question** at a time, with recommendations.
  - plan mode asks 3–4 questions, grill-me ≈25
- Never face the **grilling alone**: by question four you answer ["looks good"](https://github.com/mattpocock/skills/blob/main/docs/productivity/grill-me.md#its-a-conversation-not-an-interview).
- **Demand the cons**, never an LGTM yes-man ⚓ *server paging breaks the REST contract*
- End by having it **write QA.md** with its answers: the minutes.
- Don't **open a new chat** after grilling: those are golden tokens.

## Session hygiene

**Fresh chat per task: every prompt repays the whole history.**

- Yellow context bar = **[dumb zone](https://newsletter.pragmaticengineer.com/p/context-engineering-with-dex-horthy)**: it reports green tests that fail.
  - continue only for polish on the same files
  - cached history bills at [10%](https://platform.claude.com/docs/en/build-with-claude/prompt-caching#pricing), a cold cache at full price
- Top models show **[context anxiety](https://cognition.com/blog/devin-sonnet-4-5-lessons-and-challenges)**: near 300k they stop opening links.
- **Never paste logs**: the agent greps out three words itself.
  - ⚓ *$10,000 in one day: an Opus reading a whole log*

## Reviewable changes

**Make the agent shrink its diff: your review is the bottleneck.**

- Ask for **[Word track-changes](https://support.microsoft.com/en-us/office/track-changes-in-word-197ba630-0f5f-4a8e-9a77-3712475e806a)**, with changes minimized ⚓ *last year's compliance doc*
- [human-review](https://github.com/victorrentea/human-review) maps a PR's files to the **tests that traverse them**.
  - vibe-coded, never read: AI's best job is building your tooling

## OpenSpec

**Specs get their human reviewers before the factory runs.**

- [OpenSpec](https://openspec.dev) installs **[skills and prompts](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md)** into `.github`.
  - `proposal.md`: why and what, for the PO
  - `spec.md`: scenarios, for testers
  - `design.md`: for the architect
  - `tasks.md`: for the AI
- **Stay in markdown**: feed the factory 500+ reviewed lines, never "fix the bug".
- [Three Amigos](https://www.agilealliance.org/glossary/three-amigos/): invite the **tester with the knife** into refinement.
- Cap a spec at **one week of work**: you must review the output.
  - huge `tasks.md`: [Ralph loop](https://ghuntley.com/ralph/) or sub-agent waves

## Migration tactics

**Hand-craft the first screen, freeze what must not change, then let the agent imitate.**

- **Go slow** on the first screen: mini-prompts under a minute each ⚓ *second screen: 1 prompt, 80%*
- A **skeleton spike** beats an architecture.md.
- Polish one file by hand, then prompt **"apply my diff everywhere"**.
- Queries and business rules stay **byte-identical**: the agent never opens the DAO layer.
  - interfaces visible, implementations in a hidden Maven module
  - split the 12k-line query XML per domain first
  - ⚓ *Barcelona 100k-line PR: frozen files proven unchanged*
- Migrate and slice vertically **in one pass**: testers retest once, not twice.
- **[Lombok](https://projectlombok.org/features/GetterSetter)** saves output tokens, via a deterministic IntelliJ inspection ⚓ *$50 bet, 5000 getters*
- **Extract shared dependencies** like cached master data once, not per screen.
- **Modularize** the old app now, burning leftover quota in the month's last days.
- With ≈100 integrations, the team should drive **contract testing**: AI removes [Pact](https://docs.pact.io/)'s friction.
  - 🤖 [bi-directional](https://pactflow.io/bi-directional-contract-testing/): providers just publish OpenAPI ([openapi.yaml](https://github.com/victorrentea/petclinic/blob/main/openapi.yaml))

## Feedback loop

**AI crushes a task only when an automated loop lets it prove its work.**

- Luna clicks old and new screens side by side; **humans come last**.
- Browser agents read the **[accessibility tree](https://github.com/microsoft/playwright-mcp)**, not screenshots: cheap.
- [Playwright agents](https://playwright.dev/docs/test-agents) are **three markdown files**:
  - planner explores, writes a test plan
  - generator turns plans into tests
  - healer repairs broken locators

## Homework

**Each AI champion wires Copilot to one tool, published to team-ai.git.**

- **One champion** per tool:
  - [SQLcl](https://www.oracle.com/database/sqldeveloper/technologies/sqlcl/) ([db-cli.sh](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/petclinic-db-cli/db-cli.sh))
  - Confluence CLI ([confluence.py](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/confluence-cli/confluence.py))
  - [`az`](https://learn.microsoft.com/en-us/cli/azure/boards?view=azure-cli-latest#az-boards-query): "PBIs assigned to me this year"
  - browser CLI ([SKILL.md](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/browser-cli/SKILL.md))
- Colleagues add team-ai.git via VS Code Plugins, **[install from source](https://code.visualstudio.com/docs/agent-customization/agent-plugins#_install-a-plugin-from-source)**.

## 🎯 Main takeaways

- Every time the agent wrongs you, add one line to a markdown.
- On legacy, the team's knowledge is the asset — "we just have to validate" is why dark factories fail.
- The wrong model tier either writes slop or wastes money — pick per task.
- Grill the requirement in business words, then persist the answers.
- Hand-craft the first screen, freeze what must not change, then let the agent imitate.
- AI crushes a task only when an automated loop lets it prove its work.
- Fresh chat per task: every prompt repays the whole history.

## Corrections ❌

- Oracle lists its tables in the `V$` views ❌ — that is the data dictionary ([`ALL_TABLES`](https://docs.oracle.com/en/database/oracle/oracle-database/23/refrn/ALL_TABLES.html), `ALL_TAB_COLUMNS`); `V$` are [performance views](https://docs.oracle.com/en/database/oracle/oracle-database/23/refrn/about-dynamic-performance-views.html).
- "Sharpening the axe" is from *7 Habits* ❌ — Covey's habit is "[Sharpen the Saw](https://www.franklincovey.com/courses/the-7-habits/habit-7/)"; the axe line is usually [attributed to Lincoln](https://quoteinvestigator.com/2014/03/29/sharp-axe/).

## 🔗 Links

### Repos and tools shown
- [victorrentea/petclinic](https://github.com/victorrentea/petclinic) — the "stash of goodies" to clone
- [human-review](https://github.com/victorrentea/human-review) — vibe-coded review page, tests per requirement
- [The Agent Skills Directory](https://www.skills.sh/) — public skills; eval before adopting
- [OpenSpec](https://openspec.dev/) — spec-driven development framework

### Live exercises
- [Bug #40: Visit date has no range validation](https://github.com/victorrentea/petclinic/issues/40) — the ticket → failing test → fix demo
- [Issue #25: Add pagination to Owners grid](https://github.com/victorrentea/petclinic/issues/25) — the requirement the room grilled

### Stories
- [AAIB report: Boeing 737-400 G-OBME (Kegworth)](https://www.gov.uk/aaib-reports/4-1990-boeing-737-400-g-obme-8-january-1989) — an untrained UI change crashed a plane
- [Don't paste the AI, please.](https://dontpastetheai.com/) — review AI output before forwarding it
- [Talks History – Victor Rentea](https://victorrentea.ro/blog/talks) — the hidden-text paste demo
- [Mayday, we are sinking — what are you thinking about?](https://www.youtube.com/watch?v=KLSdOY-6R_U) — a classic on misheard requirements

---

## 📚 Session wiki

The session materials include `wiki.zip`: a small [Obsidian](https://obsidian.md) vault with **63 short, cross-linked notes**, one per idea from today (model tiers, AGENTS.md, reviewer agents, deterministic diagrams, guardrails, grill-me, OpenSpec, migration tactics…). On macOS, paste this into a Terminal — it installs Obsidian if missing, downloads the wiki into `~/Documents/ai-wiki` and opens it on **Home**:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
D=~/Documents/ai-wiki; V="$D/wiki"; Z=$(mktemp -t wiki).zip
curl -fsSL -o "$Z" "<wiki.zip link from the session materials folder>" && rm -rf "$D" && mkdir -p "$D" && unzip -qo "$Z" -d "$D" && python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)}); p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V" && open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")" || echo "Download failed: grab wiki.zip from the session materials folder"
```

Start from **Home** and follow the `[[links]]` — it's not meant to be read linearly. On Windows/Linux: unzip `wiki.zip` anywhere and in Obsidian choose *Open folder as vault* on the `wiki` folder.
