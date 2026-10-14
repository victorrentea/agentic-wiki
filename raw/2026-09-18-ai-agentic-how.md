# Agentic Engineering — Agentic.how — 2026-09-18

_generated in 6m 26s_

An open workshop with 27 engineers from across Europe, spent entirely inside a real Spring Boot + Angular codebase. The arc: pick your tool and pay for it honestly → feed the agent the right context and nothing more → make the knowledge shareable → stop trusting it blindly. Everything below was shown live on `petclinic`, branch `agentic.how4`.

## Quota

**Your quota is your ceiling: under 10,000 Copilot credits a month deserves a riot.**

- 10,000 [credits](https://docs.github.com/en/copilot/concepts/billing/usage-based-billing-for-organizations-and-enterprises) / 20 days = 500 a day = **$5/day** — one cappuccino.
- Claude Code **subscription** needs under 150 seats/company; €90 buys $5,000 of tokens. ⚓ *burn it before November*
- The usual corporate ladder is 3000 → 6000 → 12,000; some companies **auto-double** on a button.

## Vibe coding

**Vibe-code your tooling without mercy; never vibe-code software 100,000 users will run.**

- [Old definition](https://x.com/karpathy/status/1886192184808149383): never look at the code. Victor's: you **don't understand the solution**.
- His interact tool: 200k vibe-coded lines, under 100 ever read. ⚓ *read 100, got scared*

## Harness

![Slide 2 — AI in SWE: History](https://lh3.googleusercontent.com/d/1URXYVeWzSQpm4KHFf30R8igChu_lyj8I)

**The model is trained inside its own harness: Opus in Claude Code beats Opus in Copilot.**

- [Opus 4.5](https://www.anthropic.com/news/claude-opus-4-5) was the inflection point: when it fails at code, you **under-supplied context**.
- [StrongDM](https://factory.strongdm.ai) hides the acceptance tests from the implementing agent. ⚓ *professor never shows the exam*
  - worst pairing anywhere: Copilot inside IntelliJ
  - Microsoft can only copy Claude Code features into Copilot, roughly three months late

## Models

**Pick the model per task: Opus plans, Sonnet implements, Haiku and Luna chew volume.**

- Canonical use case per class, by task and not by habit:
  - [Fable](https://platform.claude.com/docs/en/models/overview) — multi-hour autonomous runs, reviewing legacy horrors
  - Opus = 40-year architect — planning and architecture
  - Sonnet = 5-year dev — coding on a budget
  - Haiku / Luna — commit messages, scanning 2 GB of logs
  - Gemini for coding — ruled out; Google never entered the coding-agent race
- Never force an expensive model to **read gigabytes raw**. ⚓ *$10,000 burned in one day*

## Prompt cache

**Reply within five minutes or pay full price again for the whole conversation history.**

- A [cache hit](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) costs **10%**; the default window is 5 minutes on Anthropic models.
- Back from lunch on a 900k-token session, typing "hi" cost **$20**. ⚓ *the $20 hi*
- A tool call, build or subagent that waits over five minutes **kills the cache** just as well.
- Thinking is **never carried over** — the end-of-turn report is what keeps the reasoning.
- [victor-statusline](https://github.com/victorrentea/victor-statusline): context size, quota, cost, **time since last turn**.
  - 🤖 Anthropic also sells a 1-hour cache TTL, at a higher write price

## AGENTS.md

**AGENTS.md is the most important file in your repo, and it needs constant trimming.**

- **AGENTS.md** is [the standard](https://agents.md/); Claude Code wants CLAUDE.md — [import](https://code.claude.com/docs/en/memory#agents-md) one into the other.
- Write for your **weakest model**; delete what training data already knows, like `mvn spring-boot:run`. ([AGENTS.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/AGENTS.md))
- A [nested CLAUDE.md](https://code.claude.com/docs/en/memory) is **injected programmatically** when the agent touches that folder. ([CLAUDE.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/petclinic-backend/CLAUDE.md))
  - a section with subsections, or over a page, gets extracted into its own file
  - rename a leftover [`.github/copilot-instructions.md`](https://github.com/github/copilot-cli/issues/489): Copilot stops looking for AGENTS.md once it finds it

## Sub-agents

**Specialist sub-agents narrow the review lens, each in its own context and its own model.**

- One concern per agent — [efficiency](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/agents/reviewer-efficiency.md), duplication, [simplification](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/agents/reviewer-simplification.md) — **model** declared in [frontmatter](https://code.claude.com/docs/en/sub-agents).
- [`/code-review`](https://github.com/anthropics/claude-code/tree/main/plugins/code-review) spawns four Anthropic-authored sub-agents; add rules in **[REVIEW.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/REVIEW.md)**, honoured by Copilot too.
  - parallel sub-agents burn three quotas

## Tripwires

**Never tell an agent something you can script — turn that rule into a tripwire.**

- "Lines under 120 chars" becomes a **[pre-push git hook](https://github.com/victorrentea/petclinic/blob/agentic.how4/.githooks/pre-push)**, silent unless violated.
- Generate, don't document: [`openapi.yaml`](https://github.com/victorrentea/petclinic/blob/agentic.how4/openapi.yaml) from the booted app, TypeScript from it. **Zero drift**.
- Agents leave **cover-your-ass comments** after deleting — hunt the traces; git history remembers.
  - "keep methods under 30 lines, especially if complex" cannot be scripted — that stays prose
  - out of tokens at work? Vibe-code tooling at home on €20, push public, pull at work

## Skills

**Skills are lazy-loaded knowledge: you pay only the frontmatter description until they fire.**

- Startup loads AGENTS.md, every skill's **[frontmatter description](https://code.claude.com/docs/en/skills)**, and all MCP tool definitions.
- Move Java-only rules into a [project skill](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/java-code-style/SKILL.md) so frontend tasks stop paying for them.
  - team rules parked in your global home folder are selfish — ship a [marketplace plugin](https://code.claude.com/docs/en/plugin-marketplaces)

## Model economics

![Slide 12 — Token Saving](https://lh3.googleusercontent.com/d/1-vAlXKjxNdDsLTzkxGUm1yemvb1UuBVe)

**Output tokens cost 5× input, so let the smart models read much and speak little.**

- Relative price: Luna is **1/40 of Sonnet**, [Opus 2.5×](https://claude.com/pricing).

## Context budget

**Everything loaded before your first word is paid for on every single turn.**

- New session, type `hi`, then [`/context`](https://code.claude.com/docs/en/context-window): **70k tokens** already gone.
- 102 skill descriptions cost 9,000 tokens — **under 40 skills**.
  - [Grafana MCP](https://github.com/grafana/mcp-grafana) alone: 10,000 tokens of tool definitions in Copilot
  - Claude Code's [tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) loads names only, not definitions

## Watch CI

**A post-push skill makes the agent wait for the build and fix it itself.**

- Polls the **[`gh` CLI](https://cli.github.com/manual/gh_run_watch)**; [Jenkins](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/jenkins-cli/jenkins.py), GitLab and AWS have equivalents.
  - weekly spend retro with [AgentsView](https://www.agentsview.io): reads history, costs zero tokens

## Brevity

**Verbose tool output is money: give every CLI you build a quiet flag.**

- **"Be brief"** in [CLAUDE.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/petclinic-backend/CLAUDE.md) buys 90% of what the caveman skill does.
- [Output style](https://code.claude.com/docs/en/output-styles) **`learning`** once a week, against skill erosion. ⚓ *it left the business logic empty for me*

## System prompt

**The system prompt is everything your harness stacks above your first word.**

- [Thinking effort](https://platform.claude.com/docs/en/build-with-claude/effort): stay **medium**, high only for Opus architecture, never max.
- Smarter model, lower effort: Opus 5 medium **equals Opus 4 high**.

## Approvals & sandbox

![Slide 11 — Agent anatomy](https://lh3.googleusercontent.com/d/12Wddagg1a4_GCtgX-GEJ2IuCym8xKLj9)

**Never pretend you understand what the agent is asking you to approve.**

- **[Approval fatigue](https://www.anthropic.com/engineering/claude-code-auto-mode)** is measurable: mistakes soar after ten approvals. ⚓ *fork bomb approved*
- YOLO inside **[Docker SBX](https://www.docker.com/products/docker-sandboxes)**: separate kernel, only fake keys inside.

## Dumb zone

*(same slide as above)*

**Past 65% of context the agent gets dumb and lies about green tests.**

- Ceilings: **150k Sonnet**, 600k Opus.
  - escape: write the plan to a markdown while still smart, then clear
- **[Hooks](https://code.claude.com/docs/en/hooks)** fire at session start, prompt, pre/post tool, and turn end. ([settings.json](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/settings.json))
## Shadow AI

**A personal subscription on corporate code has no [zero-data-retention](https://code.claude.com/docs/en/zero-data-retention) — that is a firing offence.**

- Prove smart usage instead: bosses double a quota they can see being earned. ⚓ *Tailscale to the work laptop*
- A [microVM](https://code.claude.com/docs/en/sandbox-environments) runs Docker inside → *Approvals & sandbox*.

## Marketplace

![Slide 20 — Agent Plugin](https://lh3.googleusercontent.com/d/1xb_lifusf69qtFPrflAKJM1NrKWmFIh1)

**Put the plugin marketplace on a team git, never a company-wide one, and let knowledge flow both ways.**

- **Megalomania fails** — one [Java code style](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/java-code-style/SKILL.md) for 1000 devs, when five never agree.
- Never install an internet [marketplace](https://code.claude.com/docs/en/plugin-marketplaces): [plugins](https://agent-plugins.org/) **auto-update**, pushing new scripts to every dev.
  - [skills.sh](https://skills.sh) — huge and unvetted; scan before installing, read line by line
- Ship a **feedback skill** first, so a lesson learned in the terminal opens a PR back.
  - any skill change is approved by the whole team, after an eval ⚓ *$500 in a day*

## Progressive disclosure

**Give the agent just enough to start, then a script that costs it zero tokens.**

- A skill's [script](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/jenkins-cli/jenkins.py) does work the agent **never sees** — only the result comes back. ⚓ *WhatsApp CLI*
- A **[PostToolUse](https://code.claude.com/docs/en/hooks)** hook on the Skill tool extends a company skill with your own markdown.

## Rules vs. skills

**A rule fires automatically on a file glob; a skill activation is always a gamble.**

- [Rules](https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/) are **invisible** to the agent — the harness injects them, like a hormone.
- File-path knowledge belongs in a **[rule](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/rules/pom-rule.md)**, task or workflow knowledge in a skill. ⚓ *Jira CLI*
- Non-overlapping descriptions, or activation turns random → *Context budget*.
  - write skills with Opus, not Haiku — a junior micromanaging a senior
  - [AGENTS.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/AGENTS.md) stays ≈50 lines
  - 🤖 Copilot equivalent: `.github/instructions/*.md` with [`applyTo`](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions) globs

## Jira → PR

**Never reproduce a bug alone: show it to the agent, and the agent files the ticket and the fix.**

- The [ticket](https://github.com/victorrentea/petclinic/issues/40) itself was AI-written, with **runnable [Gherkin](https://cucumber.io/docs/gherkin/reference/)** extracted from the code.
- Bug-fix prompt in three steps, the **IDE never opened**:
  - reproduce it in a browser with [Playwright CLI](https://playwright.dev/docs/test-cli)
  - write a Playwright end-to-end UI test
  - fix the bug until the test is green
- It hunted the **date format** on its own until the bug reproduced. ⚓ *"No hands!"*
- Half your tickets deserve no planning at all — move a button, add a field, change a validation: straight from **Jira to a PR**.
- Keep a private **snippet repo**, 50–100-line demos of everything you finally understood. ⚓ *[Simon Willison's TILs](https://github.com/simonw/til)*

## Grilling

**Challenge the requirement yourself, then let the agent interview you one question at a time.**

- **Saying no to the business** is what you are paid for — real data makes sorting phones absurd.
- [Plan mode](https://code.claude.com/docs/en/permission-modes) for **any non-trivial task**, but it is too weak for a first-ever feature.
- Grill-me prompt: one question at a time, each with a recommended answer, **decisions stay yours**.
  - Opus, medium or high effort — ≈$7–10, since it only talks
  - stop near question seven; have it write [`Q&A.md`](https://github.com/victorrentea/petclinic/blob/agentic.how4/Q%26A.md)

## Agent tooling

**Wire the agent into every system you have; locally, a CLI skill beats an MCP.**

- [MCP](https://modelcontextprotocol.io) only wins for **cloud or locked-down agents**. ([.mcp.json](https://github.com/victorrentea/petclinic/blob/agentic.how4/.mcp.json))
  - a [`jira-cli` skill](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/jira-cli/SKILL.md) with a personal API key covers Jira, Jenkins and Confluence ([jenkins.py](https://github.com/victorrentea/petclinic/blob/agentic.how4/.claude/skills/jenkins-cli/jenkins.py))
- A draw.io PNG carries the whole **[diagram as XML](https://www.drawio.com/blog/xml-in-png)** — parsed, it named the backend's only client.

## Context, not IQ

**You cannot outsmart Opus, only feed it context — it advised nonsense because it never saw the data.**

- Handed a **database connector**, it scanned real rows and retracted address, telephone and pets.
- Then it caught an **unstable sort**: equal last names [reshuffle between pages](https://www.postgresql.org/docs/current/queries-limit.html), so rows go unseen.
  - hence the elders' trailing ID tiebreaker; one person per three groups knows it
  - [collation](https://www.postgresql.org/docs/current/collation.html) too — does "Sz" sort before or after T?

## OpenSpec

**Spec-driven development pays past two days of work, and the framework must become yours within months.**

- Start from [OpenSpec](https://openspec.dev), [Superpowers](https://github.com/obra/superpowers) or [SpecKit](https://github.com/github/spec-kit) — unchanged markdowns after six months mean you **brought no opinion**. ⚓ *no salary raise*
- **[proposal.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/openspec/changes/paginate-owners-grid/proposal.md) for the PO**, [design.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/openspec/changes/paginate-owners-grid/design.md) for engineers — he patched the skill to split them forever.
- Spell out **non-goals** — agents always overreach.
- Markdowns are **scaffolding** — delete them once their content is rehoused ([spec.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/openspec/changes/paginate-owners-grid/specs/owner-directory/spec.md), [tasks.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/openspec/changes/paginate-owners-grid/tasks.md)):
  - functional truth back into the Jira ticket
  - acceptance criteria into pipeline tests
  - lasting architecture choices, with the why, into an [ADR](https://adr.github.io/)

## Markdown review

**Never review a markdown alone — the wrong line hides in text everybody skims.**

- "Looks good to me" is the **biggest mistake of the year** — repaid in rework.
  - nobody spotted "sort by last, then first"; the grid shows first name first
- **Two reviewers minimum**: *"you are a Sonnet when you review with an Opus."*
  - a monster markdown means the feature is too large — chop it

## Greenfield

**On greenfield, point the agent at a git repo whose code you are proud of.**

- Without one it **reinvents wheels**; the same repo is the target shape for a legacy migration plan.
- Domain you have never shipped, like mobile? Borrow a **specialist** for the kickoff.

## Reconversion

**Coding is ending; the value moves to trade-offs, guardrails and testing, not framework mastery.**

- Teams collapse into a **one-pizza trio** that ships at least 3x faster:
  - viber (often the PO) prototypes the feature
  - developer cleans the prototype up
  - QA guards latency, resilience, quality
- A job title is forming: **vibe fixer**. ⚓ *"my future profession"*

## Knowledge sources

![Slide 56 — Agentic Workflow](https://lh3.googleusercontent.com/d/1K0aIl8xsKZ3iu20BPMTaEm4f7awf_CrO)

**Feed the agent every non-code knowledge source, then let it interrogate the business.**

- Staging only, never production: **logs, database, [browser](https://github.com/microsoft/playwright-mcp)**.
  - Confluence postmortems, meeting transcripts, chat, email, incident channels
- End state: the business brainstorms **without you** — help them anyway.

## 10k PR

**A 10,000-line PR vibe-coded by the business needs a prepared answer, not panic.**

- Three ways out:
  - revoke their git permissions
  - keep it as a prototype, rebuild it properly
  - build tooling to clean it up
- Better: hand the business **your skills** and markdowns, so what arrives is not garbage.
- [Code City](https://github.com/victorrentea/code-city) renders a PR's **complexity delta** as building height and colour.

## Second LLM

**Always have a second LLM review the first's work — two dice rarely land one-one.**

- The four adversarial reviewers cost ≈**€20** on a 10,000-line PR → *Sub-agents*. ([REVIEW.md](https://github.com/victorrentea/petclinic/blob/agentic.how4/REVIEW.md))
- Finishing prompt: autofix the obvious, then report three things:
  - issues it fixed
  - issues it declined, with reason
  - assumptions taken while implementing
- Don't resuscitate a tired coder after a 40-minute Sonar build — **spawn a fresh agent**.
- **[Stacked PRs](https://www.stacking.dev/)** let the reviewer feel progress.

## Guided review

**Never read a 1,000-line PR top-down — enter through generated pictures of what changed.**

- Pictures are **printed by scripts**, zero LLM tokens, hence trustworthy.
- [human-review](https://github.com/victorrentea/human-review) tabs, all generated:
  - live Docker stack of the branch, booted in seconds
  - OpenAPI backwards-compatibility diff ([openapi.yaml](https://github.com/victorrentea/petclinic/blob/agentic.how4/openapi.yaml))
  - domain-model, database and package [PlantUML](https://plantuml.com/) diffs ([DomainModel.puml](https://github.com/victorrentea/petclinic/blob/agentic.how4/petclinic-backend/docs/generated/DomainModel.puml))
  - cost per phase, in dollars
- Start from what the agent **declined to fix**, and from its assumptions.

## Guardrails

**A guardrail the agent can delete is no guardrail — [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS) is what makes it sacred.**

- [ArchUnit](https://www.archunit.org/) binds the package diagram to the code; new coupling **fails the build**. ([PackagesArchTest.java](https://github.com/victorrentea/petclinic/blob/agentic.how4/petclinic-backend/src/test/java/victor/training/petclinic/guardrail/PackagesArchTest.java))
- Every escape earns one new line in a markdown — **reinforcement learning** for your repo. ⚓ *turret-upgrade icon*

## Corrections ❌

- "Five minutes is the cap for the prompt cache" ❌ — 5 min is the *[default](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)* TTL, not a cap.
- "Thinking is never saved" ❌ — within a turn it is [sent back](https://platform.claude.com/docs/en/build-with-claude/thinking#preserving-thinking-blocks) and cacheable; only *previous* turns are [stripped](https://platform.claude.com/docs/en/build-with-claude/thinking#thinking-block-preservation-by-model).
- "Uncle Bob invented that test-coverage metric in 1995" ❌ — his mid-90s metrics are [package coupling/instability](https://objectmentor.com/resources/articles/oodmetrc.pdf) (I, A, D), not coverage.
- "A draw.io PNG has the diagram in an XML header" ❌ — it lives in a [`tEXt`](https://github.com/jgraph/drawio/blob/744cb5420fdf126efd7a09b1d7082ca3e12c0841/src/main/webapp/js/diagramly/EditorUi.js#L6685) metadata chunk; PNG has no header.
- "Copilot reads things from the `.claude` folder anyway" ❌ — Copilot reads [`AGENTS.md`](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions) and its own `.github/` config, not `.claude/commands`.
- "GitHub's Copilot PR review is free" ❌ — it needs a paid Copilot plan and burns [13 premium requests](https://docs.github.com/en/copilot/concepts/billing/copilot-requests) per review.
- "A fork bomb destroys your computer" ❌ — a denial-of-service that saturates the [process table](https://en.wikipedia.org/wiki/Fork_bomb); a reboot recovers it.

*Stated in the room without a source on screen, so treated as notes rather than corrections: the skills.sh skill count. Two are now sourced: the scanner is NVIDIA's [SkillSpector](https://github.com/nvidia/skillspector), and its own scan of 31,132 skills found **5.2% likely malicious, 26.1% vulnerable** — so "10% are viruses" is the wrong shape, not just the wrong number. Also unsourced: the *ten*-approvals threshold (approval fatigue is real and now cited, but Anthropic's own number is that users approve **93%** of prompts — no study names a tenth request), and the claim that DeepSeek/GLM/Kimi were trained illegally on frontier-model reasoning (alleged in Jan 2025, never proven).*

## 🎯 Main takeaways

- Your quota is your ceiling: under 10,000 Copilot credits a month deserves a riot.
- Reply within five minutes or pay full price again for the whole conversation history.
- Everything loaded before your first word is paid for on every single turn.
- Never tell an agent something you can script — turn that rule into a tripwire.
- You cannot outsmart Opus, only feed it context — it advised nonsense because it never saw the data.
- Always have a second LLM review the first's work — two dice rarely land one-one.
- A guardrail the agent can delete is no guardrail — CODEOWNERS is what makes it sacred.

## 🔗 Links

Where this is going

- [Karpathy's "vibe coding" tweet](https://x.com/karpathy/status/1886192184808149383) — the original definition Victor spent the morning disagreeing with.
- [Harness engineering — OpenAI](https://openai.com/index/harness-engineering) — OpenAI's own "we stopped opening the editor"; source of the Doc Gardener idea.
- [Uncle Bob on Software Fundamentals in the Age of AI](https://www.youtube.com/watch?v=zcLPGC-tvgk) — he stopped reviewing agent code; Victor disagrees for legacy.

Watching what it costs

- [victor-statusline](https://github.com/victorrentea/victor-statusline) — the status line he taught from. Point your agent at it and say "install this".
- [AgentsView](https://www.agentsview.io/) — end-of-week retro on what each session burned, cache hits included.
- [GitHub Copilot feature settings](https://github.com/settings/copilot/features) — where the Copilot CLI toggle lives; several people in the room had it disabled and had never asked.

What the agent loads

- [AGENTS.md](https://agents.md/) — the standard every vendor but Claude Code aligned on.
- [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) — Anthropic's write-up of progressive disclosure, the interview question of 2027.
- [Agent Plugins](https://agent-plugins.org/) — the plugin standard behind the team marketplace: skills, commands, sub-agents, MCP servers and hooks in one git repo.
- [skills.sh](https://www.skills.sh/) — the largest public skill index.
- [Anthropic Courses (Skilljar)](https://anthropic.skilljar.com/) — free trainings he keeps being asked not to duplicate; the reference material behind most of today.
- [Hooks reference](https://code.claude.com/docs/en/hooks) — the exact lifecycle points where you can run your own code.
- [Explore the context window](https://code.claude.com/docs/en/context-window) — interactive walk through everything loaded before your first word, and the `/context` breakdown behind the 70k figure.
- [Tool search tool](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) — why MCP schemas stay deferred: a GitHub+Slack+Sentry+Grafana+Splunk setup burns ~55k tokens of definitions before any work.
- [code-review plugin](https://github.com/anthropics/claude-code/tree/main/plugins/code-review) — Anthropic's own four parallel reviewers, with confidence scoring, as shipped in the Claude Code repo.

Letting it run without losing the machine

- [Fork bomb](https://en.wikipedia.org/wiki/Fork_bomb) — the command nobody in the room recognised, and everybody would have approved.
- [Copilot CLI autopilot](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/autopilot) — Copilot's answer to Claude's auto mode; note that it stops asking you questions at all.
- [Docker Sandboxes (SBX)](https://www.docker.com/products/docker-sandboxes) — the YOLO box from the demo: controlled network, and a proxy that swaps in the real keys in flight.
- [Tailscale](https://tailscale.com/) — the mesh VPN behind the cautionary tale.

Spec-driven work

- [OpenSpec](https://openspec.dev/) — the framework the afternoon exercise ran on.
- [Superpowers](https://github.com/obra/superpowers) — the skills-based rival: brainstorm, then a spec shown back in readable chunks.
- [Spec Kit](https://github.com/github/spec-kit) — GitHub's take: requirements become a specification, a technical plan, and actionable tasks.

## 📚 Session wiki

The session materials include `wiki.zip` — a small [Obsidian](https://obsidian.md) vault of short, cross-linked notes, one per idea discussed today (37 notes). On macOS, paste this into a Terminal — it installs Obsidian if missing, downloads the wiki into `~/Documents/agentic-how-wiki` and opens it on **Home**:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
Z=$(mktemp -t wiki).zip && curl -fsSL -o "$Z" "https://drive.google.com/uc?export=download&id=1H9d67iDjyZbtXvWv8nTZZ1odkK_COiae" || { echo "download failed — take wiki.zip from the materials folder"; exit 1; }
rm -rf ~/Documents/agentic-how-wiki && mkdir -p ~/Documents/agentic-how-wiki && unzip -qo "$Z" -d ~/Documents/agentic-how-wiki
V=~/Documents/agentic-how-wiki/wiki
python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); [x.pop("open",None) for x in vs.values()]; k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)})["open"]=True; p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V"
open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")"
```

Start at **Home** and follow the `[[links]]` — it is not meant to be read top-down. On Windows/Linux: unzip `wiki.zip` anywhere and open the `wiki` folder as a vault in Obsidian ("Open folder as vault").
