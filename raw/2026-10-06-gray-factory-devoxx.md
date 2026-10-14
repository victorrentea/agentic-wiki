# Responsibly Dimming the Lights in the Software Factory @ Devoxx Belgium — 2026-10-06

_generated in 8m 30s_

A 3-hour deep dive on agentic engineering. Victor took one real feature of the Petclinic demo app from a vague ticket to a reviewed pull request, live with Claude Code. Along the way: what it costs, where it fails, and how to keep humans in charge while the lights go down.

## Dark factory

![Slide 4 — AI/SWE: Short History](https://lh3.googleusercontent.com/d/16hh6JgUwIAC30pU1PBKgiyTS1FhgweA7)

**In one year the industry went from vibe coding to [dark factories](https://en.wikipedia.org/wiki/Lights_out_(manufacturing)).**

- [Vibe coding](https://x.com/karpathy/status/1886192184808149383) now means **[not understanding the solution](https://simonwillison.net/2025/Mar/19/vibe-coding/)** AI ships.
- [StrongDM](https://factory.strongdm.ai/) **stopped reviewing code** ⚓ *Uncle Bob followed in April*

## Bug #40

**A clear bug needs only the [issue number](https://github.com/victorrentea/petclinic/issues/40), plus proof you can inspect.**

- `start app` replaces **a day of onboarding**.
- Ask for the **[Playwright trace viewer](https://playwright.dev/docs/trace-viewer)**: screenshots, network, payloads.

## Brain warm-up

![Slide 7](https://lh3.googleusercontent.com/d/1SsIEmTRYGnlLx6RVzvzQR5i1muMK-Je5)

**Never face an architect-class model cold: bring questions, not solutions.**

- Match **model seniority to the task**: [pagination](https://github.com/victorrentea/petclinic/issues/25) needs Opus or Sol, not Sonnet.
  - Haiku is the junior, Sonnet the mid who still errs, a local model the dog.
- Ask **what it's worth**: teams now estimate time plus token dollars.

## Grill-me

![Slide 41 — grill-me](https://lh3.googleusercontent.com/d/1LQ7T1JaXCFMtOdCpMRW7JC2Er-0cxLcw)

**Let the agent grill you, and answer like a mentor who shares context.**

- [Grill-me](https://www.skills.sh/) asked ≈22 questions, **[plan mode](https://code.claude.com/docs/en/permission-modes#analyze-before-you-edit-with-plan-mode) asks ≈3**.
- Facing A or B, **answer C** ⚓ *100k owners, saved to [AGENTS.md](https://github.com/victorrentea/petclinic/blob/main/AGENTS.md)*
- Dumb question? **Hand it your tools**: a [DB CLI skill](https://github.com/victorrentea/petclinic/blob/main/.claude/skills/db-cli/SKILL.md), no MCP.
- Ask **non-technical questions first** so the PO can attend ⚓ *who's Jason?*

## Task triage

![Slide 45 — Prompt vs Complexity](https://lh3.googleusercontent.com/d/11DNAtCpYFnLN9eSsBd-tMXgG2B-nlXkp)

**Pick the approach by task: fix bugs directly, grill in new territory, scout legacy.**

- When the whole team panics, **turn the agent into a teacher**.

## Burn the subscription

![Slide 22](https://lh3.googleusercontent.com/d/1Z6EbJWo3Yzm34fdjvkAydSHZpzGNmdqj)

**A subscription buys ≈60× its price in tokens — spend it before API pricing arrives.**

- **$100 a month bought ≈$6k** of API-priced tokens.
- Burn it on **legacy debt**: missing tests, slow builds, flaky tests.

## Own workflow

![Slide 47 — Spec-Driven Development](https://lh3.googleusercontent.com/d/1Z7WjFbuo4urx1088pWMFfSd5CfHBXWhZ)

**Pick any community spec-driven framework, then rewrite its skills until the workflow is yours.**

- **Skills unchanged for months** ⚓ *you don't deserve a raise*

## Specs on disk

**Freeze the grilling's golden tokens into markdowns you review at your own pace.**

- **Sleep on dense specs**: read tonight, skim tomorrow.
- **Cognitive debt**: each retro, AI quizzes the team on approved decisions.
- **Old specs drift** and derail agents ⚓ *vibe heresy*
  - A doc-gardener agent rescans markdowns against the code.

## Tres amigos

**Business reviews the proposal, QA the scenarios, engineers the design: [tres amigos](https://www.agilealliance.org/glossary/three-amigos/).**

- **Persist** "rewrite the [proposal](https://github.com/victorrentea/petclinic/blob/main/openspec/changes/paginate-owners-grid/proposal.md) for a PO who doesn't know JSON" into the skill.
- Gap in the [design](https://github.com/victorrentea/petclinic/blob/main/openspec/changes/paginate-owners-grid/design.md)? Ask the agent to **teach me** ⚓ *15% know collation*
  - Claude's [Learning output style](https://code.claude.com/docs/en/output-styles#learning) hands you the keyboard for the business logic.
- Spend spec markdowns on **≤20% of issues**.

## Agent context

![Slide 57 — Agentic Workflow](https://lh3.googleusercontent.com/d/1bWDTbCYL9FobnCr0WPKSjATT6kQjcz5i)

**Give the agent every source a colleague uses, not just code.**

- Sources the room listed:
  - Confluence, Jira via CLI, past PRs
  - CI failures, running app via Playwright
  - meeting transcripts, chat, email
  - production, when exposure is small

## Business PRs

**Once business sees your agent, expect 10k-line vibe-coded PRs from them.**

- Three answers to a **business PR**:
  - revoke their git rights
  - treat it as a throwaway prototype
  - vibe-fix it: "my future job description"

## Sandbox

**Sandbox the agent at OS level by default.**

- **/sandbox** keeps SSH keys out of the agent's reach.
  - Escalate to Docker or a MicroVM for system-wide changes.

## CI loop

**After a push, the agent stays and fixes until CI is green.**

- Waits past the **[prompt-cache TTL](https://platform.claude.com/docs/en/build-with-claude/prompt-caching)** re-bill everything ⚓ *CI longer than lunch*
- [Ralph loop](https://ghuntley.com/ralph/): **a fresh Sonnet per task chapter**, always in the smart zone.

## AI reviews first

**Humans review AI code only after a second, fresh AI attacked it.**

- Author agent is **sunk-cost invested** ⚓ *three lines vs. abstract factory*
- A **quorum of adversarial reviewers** ⚓ *Uncle Bob agent*
  - Reject their nitpicks rather than fixing every finding.
- Cheaper **deterministic gates** run before any reviewer agent:
  - Static analysis: Sonar, Snyk, Fortify, CodeQL.
  - Git hooks and linters — no IDE when coding from your phone.
  - A harness Stop hook vetoes a premature done.

## Reviewable output

**Make the agent pre-chew its work: bug video, assumptions, confidence.**

- Ship a **before/after bug video**, approvable while walking the dog — per [OpenAI](https://openai.com/index/harness-engineering).
- Ask **which decisions it took alone**, with confidence ⚓ *[human-review](https://github.com/victorrentea/human-review): [60%](https://victorrentea.github.io/human-review/demo/review.html)*
- Let business **criticize a running screen** ⚓ *$17 vs. two-day estimate*
  - New feature ideas get parked, not built.

## PR size

![Slide 58 — How large Should a PR be?](https://lh3.googleusercontent.com/d/1Ltzs_9HRsVgpNO2GNYFF9Gjdl4pUA7Bp)

**Thousand-line PRs get rubber-stamped — split them into stacked PRs.**

- Park curiosities in **a second agent terminal** ⚓ *Marc Nuri, Fabric8*
- Ask **Sol to review Opus** — models smell each other's work.

## Upgrade loop

**Every defect slipping past review means upgrading whatever agent or guardrail missed it.**

- **Root-cause** escaped crap before blaming the AI:
  - LGTM without reading — 3 of 4 times, per the room
  - fuzzy requirements nobody owned
  - a rule never written into markdown
- Then **upgrade the tower**: grill, reviewer, skills, Sonar rules ⚓ *tower defense*
  - guardrails the agent cannot remove: [pre-commit hook](https://github.com/victorrentea/petclinic/blob/main/.githooks/pre-commit), [CODEOWNERS](https://github.com/victorrentea/petclinic/blob/main/.github/CODEOWNERS)
- **Dim gradually**: after months of babysitting, review half the lines, then half again.

## Testing

![Slide 97 — Testing → Dare!](https://lh3.googleusercontent.com/d/1iNZ8T1K93tNDK02M7tllveL80Ihcsmrf)

**Review requirement-level tests carefully — they are the only net under AI's thousands of lines.**

- Don't review @Mock unit tests — review **[sociable tests](https://martinfowler.com/bliki/UnitTest.html)** with no mocks between classes.
- **Acceptance test coverage**: count only tests a human read.
  - harvest the IDE's "Viewed" checkbox as proof
- **Holdout tests** hidden from the agent guard pivotal flows ⚓ *[StrongDM](https://factory.strongdm.ai/), olympiad tests*

## Legacy refactoring

![Slide 98 — Refactoring → Dare!](https://lh3.googleusercontent.com/d/17nNwoSobMOdN4-0r56Wvl1JFVqVFSX6B)

**AI imitates what it sees, so repay tech debt and show examples before unleashing it.**

- **Finish half-done migrations** — the agent copies every failed initiative it finds.
- **Show, don't tell**: hand-polish one slice as ≈100 commits, then "do the rest".
- Vibe-code [OpenRewrite](https://docs.openrewrite.org/) [recipes](https://docs.openrewrite.org/authoring-recipes/writing-a-java-refactoring-recipe) ([ListGetFirstTest.java](https://github.com/victorrentea/petclinic/blob/main/refactoring-tools/src/test/java/victor/training/petclinic/rewrite/ListGetFirstTest.java)) for **syntax-safe** mass changes ⚓ *[inline single-use variable](https://github.com/victorrentea/petclinic/blob/main/refactoring-tools/src/main/java/victor/training/petclinic/rewrite/InlineSingleUseLocalVariable.java)*
- Constrain monster PRs: **SQL stays byte-identical**, checked adversarially.
  - frontier only: Opus high/xhigh, Fable reviewing

## QA agents

![Slide 100 — QA → Automate *Everything*](https://lh3.googleusercontent.com/d/1vNc9usyob6h9wAiZ6DhcW6wDD0y-bw9i)

**Testers gain most from agents: point one at a browser and let it explore.**

- Migration check: one agent clicks **old and new app side by side** ⚓ *JSF → Angular*
  - [Playwright test agents](https://playwright.dev/docs/test-agents) plan, generate and heal tests

## Factory culture

**Don't let an AI champion build one factory for everyone — each team grows its own flow.**

- Factory builders **pair daily** with a random tech lead — and log who.
- Read Claude's **PR description** first: it exposes architectural mistakes before any code.

## 🎯 Main takeaways

- **Never face an architect-class model cold: bring questions, not solutions.**
- **Let the agent grill you, and answer like a mentor who shares context.**
- **Pick any community spec-driven framework, then rewrite its skills until the workflow is yours.**
- **Humans review AI code only after a second, fresh AI attacked it.**
- **Every defect slipping past review means upgrading whatever agent or guardrail missed it.**
- **Review requirement-level tests carefully — they are the only net under AI's thousands of lines.**
- **AI imitates what it sees, so repay tech debt and show examples before unleashing it.**

## Corrections ❌

- Spring Data does not forbid serializing a `Page` ❌ — it only [warns since 3.1](https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html#core.web.page); `PagedModel` via `VIA_DTO` (3.3) is the stable alternative.
- Context windows did not stall at 1M ❌ — Gemini 1.5 Pro [offered 2M](https://developers.googleblog.com/en/new-features-for-the-gemini-api-and-google-ai-studio/) in June 2024; ≈1M remains the practical ceiling for frontier coding models.
- Stacked PRs were not invented by GitHub ❌ — they come from Google and Meta's [Phabricator](https://newsletter.pragmaticengineer.com/p/stacked-diffs) (open-sourced ≈2016), brought to GitHub by Graphite.

## 🔗 Links

**Demo tickets**
- [Bug: Visit date has no range validation · Issue #40](https://github.com/victorrentea/petclinic/issues/40) — the bug fixed live from just its number.
- [Add pagination to Owners grid · Issue #25](https://github.com/victorrentea/petclinic/issues/25) — the vague human ticket that got grilled into a spec.

**Skills & agents**
- [The Agent Skills Directory](https://www.skills.sh/) — where grill-me ranks top; skills are opinions, read them before installing.
- [Agents | Playwright](https://playwright.dev/docs/test-agents) — planner, generator and healer agents that explore an app and write its tests.

**Reviewing AI output**
- [Harness engineering: leveraging Codex in an agent-first world | OpenAI](https://openai.com/index/harness-engineering) — OpenAI's dark-factory experiment, with a video proving each bug fix.
- [victorrentea/human-review](https://github.com/victorrentea/human-review) — a skill that assembles one page for a human to review a change set fast.
- [Link Visit with Vet — human-review demo](https://victorrentea.github.io/human-review/demo/review.html) — sample page: assumptions, open review points, confidence.

## 📚 Session wiki

The session materials include `wiki.zip` — a small [Obsidian](https://obsidian.md) vault with **43 short, cross-linked notes**, one per idea discussed (dark factory, grill-me, tres amigos, specs on disk, the agentic workflow map, adversarial review, deterministic gates, stacked PRs, the upgrade loop, holdout tests, legacy refactoring…), with the discussed slides inside. Browse it online at [interact.victorrentea.ro/st32qk/wiki-site](https://interact.victorrentea.ro/st32qk/wiki-site/), or on macOS paste this into a Terminal — it installs Obsidian if missing, downloads the wiki into `~/Documents/devoxx-agentic-wiki` and opens it on **Home**:

```bash
ls -d /Applications/Obsidian.app >/dev/null 2>&1 || brew install --cask obsidian
D=~/Documents/devoxx-agentic-wiki; V="$D/wiki"; Z=$(mktemp -t wiki).zip
curl -fsSL -o "$Z" "https://drive.google.com/uc?export=download&id=1bfAXAPlAk4ZP5hDAlN7NA52breWAW_y1" && rm -rf "$D" && mkdir -p "$D" && unzip -qo "$Z" -d "$D" && python3 -c 'import json,os,sys,time,pathlib; v=os.path.abspath(sys.argv[1]); p=pathlib.Path.home()/"Library/Application Support/obsidian/obsidian.json"; d=json.loads(p.read_text()) if p.exists() else {}; vs=d.setdefault("vaults",{}); k=next((kk for kk,x in vs.items() if os.path.abspath(x.get("path",""))==v),None) or format(int(time.time()*1000),"016x"); vs.setdefault(k,{"path":v,"ts":int(time.time()*1000)}); p.parent.mkdir(parents=True,exist_ok=True); p.write_text(json.dumps(d))' "$V" && open "obsidian://open?path=$(python3 -c 'import urllib.parse,sys;print(urllib.parse.quote(sys.argv[1],safe=""))' "$V/Home.md")" || echo "Download failed: take wiki.zip from the session materials folder"
```

Start from **Home** and follow the `[[links]]` — it isn't meant to be read linearly. On Windows/Linux: unzip `wiki.zip` anywhere and open the `wiki` folder as a vault in Obsidian ("Open folder as vault").
