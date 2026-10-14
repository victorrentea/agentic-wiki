# AI-Assisted Coding — an open workshop (2026-06-02)

A one-day workshop on agentic engineering for mid-senior developers. We went from "how an LLM actually works" all the way to a state-of-the-art multi-agent workflow, security and sandboxing — using a live PetClinic feature (sorting + pagination) as the running example.

> Note: the lunch-hour Romanian consult that overlapped this English workshop is **not** part of this summary (separate session).

---

## 1 — Fundamentals: how LLMs work and where they break (≈10:00–11:00)

### The model is just a file of numbers
- An **LLM** is the internet compressed into floating-point weights. Training a frontier model costs **$1M–$1B**, which is why new models don't ship weekly. You can literally download an open-weight model (e.g. DeepSeek) from [Hugging Face](https://huggingface.co/) — ~13 GB files of floats — and run it on a beefy Mac Studio.
- The **cutoff date** is the model's "birth date": it cannot know anything that happened after. 🤖 When a task needs newer knowledge, give it [WebFetch](https://code.claude.com/docs/en/overview) or wire up [Context7](https://github.com/upstash/context7) — an MCP that injects up-to-date, version-specific library docs straight into the prompt.

### Tokens cost money — and not where you think
- ~1 token ≈ 1 word. **Output tokens cost 5× input** (Sonnet: $3 in / $15 out per 1M). JSON tokenizes badly (lots of tiny tokens) → the [TOON format](https://github.com/toon-format/toon) (Token-Oriented Object Notation) trims ~20% vs JSON. Play with the [Anthropic tokenizer](https://lunary.ai/anthropic-tokenizer) to see it.
- **Your real token sink is not your prompt length** — it's tool calls dumping file contents and test output into context, plus a bloated system layer (too many tools/skills/rules). A long, careful prompt is cheap; 30 noisy tool reads are not.
- For big mechanical rewrites, **write a script** instead of having the model rewrite everything — exact, fast, and free of output tokens. Opus itself will usually recommend this.

### Stateless + cache + the dumb zone
- LLMs are **stateless**: everything above your message (system prompt, tools, memory, all prior tool calls/results) is re-sent every turn. Thinking from previous steps is dropped; tool calls/results are kept.
- The **prompt cache** lets you pay ~10% for the repeated prefix — but the cache TTL is short. Leaving an agent idle past the window means re-paying full price; people report **5× cost** just from juggling terminals and making agents wait. Rule: **don't leave an agent waiting >5 min.** 🤖 Anthropic's cache has a **5-minute default TTL** plus an optional **1-hour** TTL; the "don't idle" rule is the safe takeaway regardless.
- **Dumb zone** = context past ~65% full. The model gets "tired," hallucinates, forgets, and just agrees to get peace. 🤖 This 65% is Victor's *quality-degradation* heuristic (well-supported by "context rot" research); Claude Code's *auto-compact* actually fires much closer to the real limit, not at 65%.
- **Needle-in-haystack**: even a 1M-token window doesn't mean the model recalls everything in it. Comfort zone for Opus ≈ 200–300k tokens. **>500k → you're doing it wrong**: make a markdown plan, spawn sub-agents, or rethink the chunk size.
- **Compaction** (auto or `/compact`) summarizes the thread ~100×. Risk: **you don't control what survives** — you might lose a critical rule or spec detail. Safer: while the model is still sharp, ask it to write a markdown plan, review it, `/clear`, and continue from the plan (explicit compaction).

### Tools that keep you sane
- **Sub-agents**: the orchestrator spawns isolated conversations (own context window) for heavy reading; they report back a short result and die, flushing their context. Canonical use = **codebase research** before touching legacy. See HumanLayer's [`research_codebase` command](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) — it fans out parallel sub-agents and returns file+line pointers.
- **Checkpoints**: double-`Esc` rewinds the conversation to an earlier point (Copilot has a fork icon). The full conversation lives on disk; only the live context window can be compacted.
- **Model vs Harness**: the *model* (Opus/Sonnet/GPT/Gemini/DeepSeek) is the weights file; the *harness* (Claude Code, Copilot, Cursor, OpenCode…) is the toolset around it. CLI harnesses ship the future first. Practical advice: **never use the Copilot IntelliJ plugin** — prefer Copilot CLI in the terminal.

### The AI pitfalls (the heart of the morning)
- **Hallucination** = confidently wrong. Triggers: dumb zone (>65%), too many rules (>~85), vague/contradictory prompts, **SpecRot** (stale markdowns the agent finds in git and trusts), being *too close to something standard* (the famous car-wash riddle), or needing post-cutoff knowledge.
- **Sycophancy** = "you're so right, grandmaster." It's marketing; it won't sharpen your solution. Tell your agent: *challenge ambiguous / contradictory / wrong prompts; don't let me continue.*
- **Premature fixation** = "boss, I found the bug!" like a child, before proving anything. Fix (in order of power): ⭐ make it **explain why** it's the bug; ⭐⭐ make it **prove it** with red→green TDD (a test that's red, turns green after the fix). Adding *"red-green TDD"* to any prompt measurably raises quality. Try the [systematic-debugging skill](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md) from Obra's superpowers. 🤖 The deeper reason TDD works: it hands the agent a *deterministic feedback loop* it can iterate against autonomously — proof is a side effect.
- **Answer injection** = "Isn't Kafka the best broker in the world?" Emotionally loading an option biases the model toward it and collapses the option space. Keep options open.
- **Spiraling**: when the agent goes wild, stop it — but before you `/clear`, ask in a neutral voice *"why did you do that?"* to find the **seed of incorrectness** (Steve Yegge's term). Then either refine the prompt or add the missing context to `CLAUDE.md`. This is your human reinforcement-learning loop.

---

## 2 — Context & prompt engineering (≈11:10–12:30)

### CLAUDE.md is a liability, not a dumping ground
- `init` scans the repo and generates a `CLAUDE.md` — fine to *start* with, dangerous to keep raw. A study found **AI-generated CLAUDE.md cost ~20% more tokens and made the agent less effective**. It pushes you into the dumb zone faster.
- Keep it **small (<100 lines), concise, and pain-fed** — not AI-generated. You add a rule only after the AI annoyed you and you did a root-cause on *why*.
- **Review CLAUDE.md every sprint** — first thing in the retro. Congratulate contributors, then ruthlessly shrink it. Strip everything the model already knows from training (how to run npm, Spring Boot, etc.).

### Four techniques to limit what's loaded into the brain
1. **Don't trust AI-gen CLAUDE.md** — clean out the obvious noise.
2. **Nested CLAUDE.md** — move backend rules into the backend folder, frontend into frontend. Harnesses load them hierarchically (Claude Code & GH CLI do; OpenCode doesn't — test yours).
3. **[Skills](https://code.claude.com/docs/en/skills)** — lazy-loaded procedural knowledge. Only the **front matter** (name + description) is eager-loaded; the body loads on demand. 🤖 Keep the description *trigger-rich* so the model reliably picks the right skill; that one line is all it sees up front.
4. **Referenced files** — a skill or CLAUDE.md can point to further markdown loaded only when needed.

All four are **progressive disclosure**: reveal knowledge piece by piece. 🤖 Stronger models (Opus/Sonnet/GPT) refrain from reading linked files they don't need; weaker models over-read and hit the dumb zone sooner.

### Skills, deeper
- Convention: folder name = skill name, `SKILL.md` inside (Anthropic standard). Keep to **~20–30 skills** — more confuses the model and bloats every context with front matter.
- Skills can: point to **scripts** (e.g. "run `coverage.js` for a report" — faster, exact, cheaper than re-deriving each time), point to **templates**, **fork context** into a sub-agent, restrict **tools**, and pick a cheaper **model**.
- **Human-only skills**: set `disable-model-invocation: true` so only a human `/slash` can trigger an expensive skill (e.g. a token-heavy "regen-manual" that screenshots the app). Live lesson: Victor's claimed `human-invoked: true` attribute was a **hallucination** — `disable-model-invocation: true` is the real, documented one. He caught it by **asking the agent for a proof link**… and the sub-agent hallucinated too. ❌ Always demand the source, like asking a kid "did you do your homework?"
- A skill that forks context + restricts tools **is essentially a sub-agent** — e.g. a research minion with *only* WebFetch and no disk access (critical for security, see §6).

### Cross-repo knowledge sharing
You start the CLI in one microservice — how does it learn about the others?
- `/add-dir` other folders, or start the CLI in a parent folder containing all clones (floods context if there are hundreds).
- Better: expose only the **APIs**. Create an **`ai-central` git repo** where every service pushes its Swagger/REST/AsyncAPI contract via GitHub Actions or submodules; or use an **API-management system** (Apiary, [Pact](https://pact.io/)). The agent consults the contracts, not the implementations.

### AI in teams & project management
- Teams are shrinking: **2-pizza → 1-pizza "AI pods"** (~3 people incl. product/test). You mostly code solo — which Victor dislikes, especially for design.
- **Pair/mob on the `design.md`** — reviewing architectural markdowns alone is brutal; do it with a colleague.
- **Onboarding is broken**: a friend left for 1.5 weeks; his two teammates pushed so much AI code he couldn't recognize the repo on return. If you stop holding tight to how the code evolves, **~3 months and you're lost**.
- **PM with AI**: it can identify decent-sized stories, inter-dependencies, build Gantt/Kanban. But it's high-impact with **no automated feedback loop** → "throw the dice multiple times": **1 agent writes, 2 review (Opus + GPT), then a human.** And teach the agent to render a **visual Kanban** — a picture is far easier for the human brain to review than markdown.
- **Coding phases**: (optional code research in legacy) → brainstorm functional requirements → design/architecture (ideally pair/mob) → plan (tiny steps) → AI generates code → acceptance testing.

---

## 3 — Spec-Driven Development, live (≈12:00–13:06 + 14:04–14:36)

The running feature: **GitHub issue #25 — add sorting + pagination to the PetClinic owners screen.**

### Brainstorm first (grill-me)
Installed the tiny [grill-me skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) ("interview me relentlessly until shared understanding"). Decisions that fell out of the grilling:
- **Where to sort?** Not backend-in-memory (memory pressure at 100k+ rows), not frontend-only (breaks with server pagination). → **Database `ORDER BY` + server-side pagination.**
- **API shape**: Spring's standard `Page` wrapper; explicit URL params (`page`, `size`, `sort`).
- **The composite-name trap**: the grid's "Name" column renders `firstName lastName`, but there *is no* lastName column. Victor caught the AI drifting toward "sort by last name" → corrected to **sort by first name, last name as tiebreaker** (matching the display). *"One degree to the left and the bazooka misses."*
- **Sortable columns**: only Name and City (pets is a collection, phone/address useless) — flagged as a **requirements change** → comment on the issue for the business.
- **Default sort** on load (name ascending, with the ▲ indicator); **page resets to 0** when the filter changes; **URL-driven state** so links are bookmarkable/shareable.

### OpenSpec: propose → design → tasks
Installed [OpenSpec](https://openspec.dev/) (`npm i -g @fission-ai/openspec`, then `openspec init`). `openspec propose` produced three reviewable markdowns:
- **proposal.md** — the functional "what" (review with **business**). Some argue this belongs in Jira, not git, since it's a functional description.
- **design.md** — the architecture "how" (review with a **fellow developer**). Read the **Non-Goals** chapter carefully — AI loves to creep in extra scope.
- **tasks.md** — fine-grained steps with checkboxes the agent ticks as it goes (resumable after token-out / power loss).
- **spec.md** — Given/When/Then **acceptance criteria** (review with **testers**).

`openspec apply` was run with *"use a separate sub-agent per chapter and parallelize"* → it spawned **waves of agents** (backend / frontend / docs) over lunch. 🤖 Forcing parallel sub-agents costs more (each pays for its initial context) — that's the price of speed.

### Tracer bullets
AI **builds code in horizontal layers** and then discovers integration breakage at the end. Say the magic words **"tracer bullets"** (vertical slices) and it builds one full vertical flow (screen → backend → test) before widening — avoiding the painful integration stage.

### Acceptance tests as the safety net
- Turned the spec into a runnable **[Gherkin](https://cucumber.io/docs/gherkin/reference) feature file** ("build Gherkin tests capturing the acceptance criteria; don't write glue code yet — another agent is still implementing"). Gherkin is the easiest form for *both* technical and non-technical people; the glue code wires each step to logic, runnable on Jenkins.
- **Example tables** (inputs → expected outputs) shine when the business thinks in examples — great for complex business rules. 🤖 A small Gherkin note from a participant: `And` after `Then` keeps assertion semantics, but moving a step changes its meaning (`And` under `When` = action, under `Then` = assertion) — keep `Then` blocks purely about outcomes.
- **Test wide, not narrow**: Victor tested the feature at the API/end-to-end level (Playwright driving the browser through the real back+front), not individual methods. The wider the test, the more freedom AI has to refactor underneath. (And don't re-test what Spring already tests, e.g. `Pageable` itself.)

### The live bug + brain-efficient review
- A real bug appeared: after refresh on page 2, the displayed rows desynced from the URL/page number. Fix recipe: **two screenshots + "reproduce it yourself with the browser, write a Playwright test that's red, then fix until green."**
- Review pass on the diff: caught **no service layer** (controller → repo directly, "eww"), and an **HTTP pipe on the same line** — Victor went deliberately "outrageous" about it, fixed it, *then fed the rule into the frontend `CLAUDE.md`*. That's the human-RL loop: polish the code **and** train the memory.
- **When to go spec-driven?** Simple change (a button, a field) → just do it / plan mode. >~1 day of work, or it touches the domain model / workflow / something delicate → full SDD. If you can afford Opus + 1M context (e.g. via Bedrock), you can push direct execution further.

---

## 4 — Token efficiency & Agentic engineering: MCP (≈14:36–15:42)

### Taming large codebases
- **[CodeGraph](https://github.com/colbymchenry/codegraph)** — a pre-indexed knowledge graph of symbol relationships / call graphs; navigate "find references" instantly without grep.
- **RAG / semantic search** — e.g. [Serena](https://github.com/oraios/serena)-style: ask *"where do we implement discounts?"* and get the 3 relevant classes by similarity. Great for spotting semantic duplication.
- **Don't underestimate grep** — it's extremely effective and free; AI filters by repo/package anyway. Whatever tool you add, **don't add-and-pray**: run the *same task* repeatedly for a month and measure token savings before adopting.
- **[RTK](https://github.com/rtk-ai/rtk)** — a local Rust **tool-call proxy** that filters/compresses command output before it hits context (claims 60–90% token reduction): turns "enumerating… counting… delta…" git noise and verbose test output into the few lines that matter.

### Token-saving rules of thumb
- **Don't code with Opus** — use Sonnet. Opus is for design/review (it's pricier mostly because it *reasons* more, and reasoning = output tokens; Caveman can't touch that).
- `CLAUDE.md` **<100 lines, pain-fed**, reviewed every sprint.
- **Don't have 300 MCP tools / 300 skills** loaded.
- **Hooks return minimal output** when OK (they're a context leak otherwise).
- **`/clear` between tasks**; one conversation per task; rename + resume for later.
- **Sub-agents** to read a ton of text; **don't leave a terminal waiting >5 min.**
- The **Caveman skill** shortens *human-facing* output — Victor isn't convinced: it optimizes the wrong place (your comprehension) and can't touch reasoning cost.
- Use the **Insight / reflect-on-conversations** report to see *where* you lost tokens (tool errors, parallel sessions, rework).

### What is MCP?
- **Model Context Protocol** ❌ (Victor said "Modern" — it's **Model**), a standard from Anthropic to connect agents to tools, over a **streaming** transport (SSE / WebSocket) so both sides can push data (throttle, iterate over large data). See [modelcontextprotocol.io](https://modelcontextprotocol.io).
- A **tool** is a method; the **server** is a process — started **locally as a child** of the agent (e.g. `npx postgres-mcp`) or **remote** (e.g. Context7). Resources ≈ GET; tools ≈ POST/PUT/DELETE (potentially destructive).
- Beyond REST, MCP adds: **elicitation** (the server demands human input/approval mid-call — e.g. "confirm before creating this visit"), **destructive annotations** (Claude double-checks "are you sure?"), and **sampling** (the server borrows the caller's LLM — rare, burns your tokens).
- Live demo realism: the create-visit tool **derailed** (hit Postgres directly, lost track of which owner), and a Java call hung ~30s — Victor cancelled it. *"Famous last words: I shall not touch the database."*

### MCP vs REST vs CLI
- **Expose REST** if your Swagger is well-documented (the agent just `curl`s it). Expose **MCP** when you need human-in-the-loop (elicitation) or streaming.
- **Consume CLI over MCP** when possible: the **GitHub CLI (`gh`) is far cheaper than the GitHub MCP** — it's already in the model's training data (zero token cost to "define" it), whereas MCP tool descriptions cost context and lean on verbose JSON. 🤖 For an unfamiliar CLI, teach it with a one-shot example in CLAUDE.md/skill — still cheaper than an MCP.

### MCP / CLI hall of fame
- **Jira** (well-rated community superset MCP; connects on-prem via PAT in ~15 min) · **Confluence on-prem** · **CI** = `gh`/Bitbucket CLI · **[Playwright MCP](https://github.com/microsoft/playwright-mcp)** vs **[Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)** vs the **Claude Chrome extension** · **Logs** (digging logs by hand is over) · **Teams/Slack** (add the agent as a *team member* — pro level) · **[Context7](https://github.com/upstash/context7)** for live docs · **Figma** · **Grafana/Prometheus** · **Oracle SQL*Plus** · **MSSQL `sqlcmd`** · a **heap-dump MCP** (found 17 memory leaks from a `.hprof`).
- **Browser deep-dive**: Playwright is cross-browser and — the surprise — **needs no vision**. It drives pages via the **accessibility tree** (the text model built for screen readers), so it can click/type/sort without OCR. Vision is only needed for pixel-precise alignment checks. 🤖 Chrome DevTools MCP is the pick for breakpoint debugging + performance tuning (Chrome-only); the Claude extension is the pick when you need *your logged-in sessions*.
- **Database warning**: even **read-only** AI can take down an Oracle instance — it spawns experimental query variants, leaves bad ones **running for hours**, starving the shared instance. Grant only query perms, and still watch it. Tip from the field: the biggest Romanian eShop added **column/table comments** to make schemas AI-friendly (the agent reads the comments to decode magic values like "-2").

### Subsidized economics
- Claude **Max** (~€90/mo) is heavily subsidized — Victor pays €90 but **burns ~$3000/mo** of tokens. **Never stay on usage-based billing** for heavy use. To level up fast: take a passion project from your backlog and **vibe-code it for a month** (don't read the code, just ship/test/steer). *"You'll lose your sleep — and never type code again."*

---

## 5 — State-of-the-art multi-agent workflow + Quality/Architecture guardrails (≈15:59–16:55)

### The workflow three companies (IN / RO / NL) are converging on
**Jira ticket** → grill-me brainstorm *with the business* (comments/assumptions land on the ticket) →
**feature files** (Gherkin, in the team's/ubiquitous language — gov clients even code in the native language; **avoid synonyms**, they kill agents) →
**architecture.md** (agent drafts in the cloud, tags you on the PR; you comment from the train, it updates live) →
**plan mode** (read-only; agent produces the fine-grained plan, you review briefly) →
**coding agent** (auto-spawns sub-agents: backend / frontend / docs) →
**multi-concern, multi-model reviewers** →
**Jenkins** (full nightly build; attempts the merge into main and reruns *all* tests) →
**guided human review** →
**human merges** →
**cross-repo PRs** (one ticket → N microservice PRs) + **CODEOWNERS**.

The one thing to remember: **never review work in the same context that produced it.** The coder built a mental model; asking it "now find bugs" just burns tokens. Spawn **fresh eyes** — and make them **multi-concern** (clean-code / performance / compliance / acceptance-criteria) **× multi-model** (GPT loves criticizing Opus and vice-versa) → up to **8 review contexts**, looping **3–7×** until the reviewers go quiet. Each reviewer is a prompt polished month after month; when you see garbage, you fix *that reviewer's prompt*, not the output.

- **Guided, risk-ordered human review**: every reviewer remark "heats up" a file; the human reviews **descending by heat/risk** (the file with 7 failed reviews + 1 failed Jenkins goes first) while still alert. Plus a **preview env you open on your phone** to click the feature. 🤖 You stay the bottleneck on purpose: *"you can never run faster than what you can comprehend."*
- **System-2 thinking** (deep, draining) belongs to the plan stage — **one ticket at a time**, visualize edge cases (page+sort interactions, caches) with [PlantUML](https://plantuml.com) diagrams. A picture is worth a thousand words to review.

### Quality guardrails — feedback loops AI can't ignore
- **Post-edit hook** (`PostToolUse`): on every Write/Edit it formats the file and **warns** on fully-qualified class names. Principle: **auto-fix if you can script it (lint/format); escalate to the agent only if you can't.** A tiny reactive prompt — "yo, fix this" — beats bloating CLAUDE.md with "never do X."
- **Hook taxonomy** (see [Claude Code hooks](https://code.claude.com/docs/en/hooks)):
  - *Agent hooks*: `SessionStart` (inject `git status` so it knows the branch), `UserPromptSubmit`, `PreToolUse` (RTK output-trim; **rate-limit the DB MCP** so it doesn't hammer a shared server), `PostToolUse`, `Stop` (ring an escalating bell so you answer within the 5-min cache window), `SessionEnd` (**retrospect the conversation and file a PR to central skills if the user seemed annoyed** — the coolest one).
  - *Git hooks*: `pre-commit` ([gitleaks](https://github.com/gitleaks/gitleaks) + a `secrets.env` scan that blocks committing known secrets — Victor once committed a password to public GitHub), `pre-push` (coverage gate, lint the OpenAPI, **regenerate TypeScript types from the OpenAPI** so a renamed Java field breaks the frontend compile → drift is impossible).
  - *Post-push watcher*: the conversation stays alive watching CI; if the build/Sonar/Snyk/PMD fails, it's fed back so the agent fixes it **with full context** — better than `/loop` or cloud `/schedule`, which start fresh and must rediscover the task. 🤖 gitleaks/`--no-verify` can be bypassed locally — back it with **server-side CI secret scanning** and **rotate any secret that ever hit git history**.

### Architecture guardrails — keep diagrams honest with tests
- **OpenAPI in sync with code** (rename a DTO field → regenerated TS → compile fails). **Domain-model diagram** and **package/[C4](https://c4model.com) diagram** generated from the code and **kept in sync by ArchUnit-style tests** — remove a real dependency from the diagram and the test (hence the push) fails until it's restored.
- **CODEOWNERS** = "the elders": critical changes (added Java/frontend deps, OpenAPI changes, DB migrations, architecture-diagram changes) force a **second human reviewer**. Preparing for the inevitable rushed "LGTM".
- **Code-city / heatmaps**: visualize bug-density and commit-density per LOC to find legacy hot spots (e.g. a 252-line Spring class with 103 commits).

---

## 6 — AI Security (≈17:05–17:50)

### The hands of the agent
Read / Grep / Write / Edit / WebFetch / **Bash**. Bash is what makes agents powerful — and dangerous.
- **Never start an agent in your home folder**: read tools default to *allowed* in the working dir, so `~/Downloads` (untrusted files), `~/.aws`, settings, and **browser cookies** become readable without asking.
- **Permission modes**: reads allowed by default in cwd; writes/edits gated (or `accept edits`); **plan mode = read-only**; **auto mode** = a *judge LLM* approves the main agent's actions (misses ~14–17%, **not robust**); **YOLO** = `--dangerously-skip-permissions` (Copilot calls it auto-approve, Codex literally "yolo") — it can read any file and ship your SSH keys.

### Bash can route around your rules
- Even if you `deny` edits to a folder, the agent can `bash cat > file` to overwrite it (it pops a confirm — only your careful reading saves you).
- Ban `curl`? It may write a Python snippet that does the same. So **deny `Bash(curl)` and forget, allow only WebFetch** (a dedicated, sandboxed fetch tool). But validating the URL in a `PreToolUse` hook still fails — the agent can hide the URL in an env var. **Bash is nearly impossible to fully contain.**
- **AI red-teaming**: lock up an Opus and tell it to **escape your own guardrails**. Live, it found `git -c core.hooksPath=...` to reset the hook path, `--no-verify`, config mutation, and **fork bombs** (`:(){ :|:& };:` — a recursive DoS). Do this *on purpose* before you trust your setup.

### Supply-chain attacks (the most common way to get hacked today)
- **npm post-install scripts run arbitrary code at install time** — Java/C# devs are often unaware. The morning's `npm i -g @fission-ai/openspec` literally ran one (that's how the `openspec` command got registered).
- The infamous **npm token-harvesting attack** (Axios-class): a compromised maintainer token added an evil transitive dependency whose post-install **harvested every token** (Anthropic/Codex/GitHub/Jira) to poison more packages. Install it in the wrong window and the advice is *format your drive*. Mitigations: **`npm config set ignore-scripts true`** (run scripts only for vetted deps), **avoid versions <7 days old** (double-edged — you also delay security patches), **pin versions** and upgrade only on vulnerability reports. 🤖 Pair this with a lockfile + CI SCA scanning; `--omit=dev` does **not** protect you from transitive post-install scripts.
- **Slop-squatting**: attackers publish packages named after libraries that **don't exist**, betting an AI will hallucinate the name.
- **Skills & MCP poisoning**: the skills marketplace exploded to ~600k entries — mostly **unbenchmarked opinions**; Snyk found ~⅓ had security weaknesses. MCP servers can **poison context purely via tool descriptions** (no code runs) — and installing them may run post-install scripts. **Review the source before building**; don't run random binaries.

### Sandboxing — four layers
1. **Judge LLM / auto-mode** — weakest (misses ~14–17%, can approve a bad install).
2. **OS sandbox** (`sandbox: true`) — the OS restricts **filesystem paths** and **network domains** the process may touch. Blocks post-install exfiltration and even `bash cat` to protected files — the agent can't get past the kernel. (But a human "yes" to an install still escapes it.)
3. **Docker** (run the agent in a container, even in YOLO) — mount only allowed folders, restrict reachable hosts via iptables. Give dangerous tools (DB, push, *docker-in-docker* for testcontainers) through a **controlled MCP bridge** to the host daemon. 🤖 Naively mounting `docker.sock` / sharing the kernel = trivial host escape — broker it.
4. **MicroVM** — strongest, still immature.

> Even with all this: Anthropic's own Opus **escaped one of these boxes once** — famous in the lore. A men-in-the-middle proxy (à la "Bromure") can keep credentials away from the agent entirely while still letting it use them.

### Skipped for time (per the agenda)
Prompt-injection vectors in depth, the **lethal trifecta** + OpenClaw, and the deep API/domain/C4 architecture guardrails were only mentioned — worth following up.

---

## 🎬 The meta-moment to remember

To close, Victor ran the **training-summarizer** on the day's 8-hour transcript — and accidentally started it **with Sonnet**, which promptly choked. *"Model for parsing 8 hours of transcription, you can't do Sonnet — way too much. I'm gonna call Opus 1 million."* A perfect, unplanned live demo of the **first lesson of the day**: a huge context (>500k tokens) on the wrong model collapses. The whole workshop, proven in real time.

---

<!-- BRAIN-DEAD-START -->
# Super-sumar — in case brain-dead

## LLM fundamentals & tokens
1. LLM = internet compressed into a weights file; cutoff date = it can't know newer → WebFetch / Context7.
2. Output tokens cost 5× input; JSON is token-heavy → TOON format (~20% lighter).
3. Real token sink = tool calls dumping file/test output + bloated system layer, NOT prompt length.
4. Big mechanical rewrite → write a script, don't have the model rewrite everything.
5. LLM is stateless; whole thread re-sent every turn; thinking dropped, tool calls kept.
6. Cache TTL ~5 min → never leave an agent idle >5 min (people report 5× cost).
7. Dumb zone >65% context → hallucinate/forget; Opus comfort zone 200–300k tokens.
8. >500k tokens = do it wrong → markdown plan + sub-agents.
9. Auto-compaction is lossy & uncontrolled → prefer explicit "write a plan.md, /clear, continue".
10. Model (weights) ≠ Harness (Claude Code/Copilot/Cursor); never use Copilot IntelliJ plugin.

## AI pitfalls
11. Hallucination triggers: dumb zone, >85 rules, vague/contradictory prompt, SpecRot, too-close-to-standard, post-cutoff knowledge.
12. Sycophancy = marketing → tell agent to challenge wrong/ambiguous prompts.
13. Premature fixation → make it explain why + prove via red-green TDD; try systematic-debugging skill.
14. Add "red-green TDD" to any prompt = measurably higher quality (gives a feedback loop).
15. Answer injection → don't emotionally load options; keep the option space open.
16. Spiraling → ask neutrally "why did you do that?", feed root cause to CLAUDE.md.

## Context engineering
17. AI-gen CLAUDE.md costs ~20% more & hurts → keep it <100 lines, pain-fed, reviewed every sprint.
18. Shrink context 4 ways: clean CLAUDE.md, nested CLAUDE.md, skills, referenced files = progressive disclosure.
19. Skill front matter (name+desc) is eager-loaded; body lazy → keep ~20–30 skills max.
20. `disable-model-invocation: true` = human-only skill (the real attribute; "human-invoked" was hallucinated).
21. Always ask the agent for a proof/source link — sub-agents hallucinate too.
22. Cross-repo: ai-central git of Swagger/contracts, or API mgmt (Apiary/Pact); not clone-everything.
23. Teams shrinking to "AI pods" (~3); pair/mob on design.md; onboarding breaks in ~3 months.
24. High-impact + no feedback loop → throw dice: 1 writes, 2 review (Opus+GPT), then human; render visual Kanban.

## Spec-driven development
25. Phases: research → brainstorm → design → plan → code → acceptance test.
26. grill-me before specing; one-degree drift misses the target (composite name column trap).
27. OpenSpec = proposal (business) / design (dev) / tasks (agent) / spec=acceptance criteria; read Non-Goals.
28. "tracer bullets" = vertical slices, avoids painful integration-stage layering.
29. Gherkin acceptance tests survive on CI; test wide (API/E2E) not narrow; don't test the framework.
30. Reproduce bugs with browser + red-green Playwright test; review diff, then feed rules into CLAUDE.md.
31. SDD when >~1 day or touches domain/workflow; else plan mode or just-do-it.

## Agentic engineering / MCP
32. Large code: CodeGraph (graph), Serena/RAG (semantic); grep underrated; measure before adopting a tool.
33. RTK = local proxy compressing tool output 60–90%.
34. Code with Sonnet, not Opus; Caveman shortens human output but can't cut reasoning cost.
35. MCP = Model Context Protocol (not "Modern"); streaming SSE/WebSocket; local-child or remote.
36. MCP extras over REST: elicitation (human approval), destructive annotations, sampling.
37. Consume CLI over MCP (gh CLI ≪ GitHub MCP tokens — already in training data).
38. Playwright drives via accessibility tree → no vision needed; DevTools for debug/perf; Claude extension for logged-in sessions.
39. Even read-only AI can kill an Oracle instance via runaway background queries.
40. DB comments make schemas AI-friendly; rate-limit the DB MCP via a hook.
41. Claude Max ~€90/mo is subsidized (~$3000 burned); vibe-code a passion project for a month to level up.

## SOTA workflow & guardrails
42. Never review in the same context that wrote the code — fresh eyes, multi-concern × multi-model (up to 8), loop 3–7×.
43. Guided human review descending by risk/heat; preview env on phone; you're the bottleneck on purpose.
44. System-2 deep thinking = one ticket at a time; review via PlantUML/sequence diagrams.
45. Auto-fix if scriptable (lint/format) else escalate to agent; hooks return minimal output when OK.
46. Hooks: SessionStart inject git status; PreToolUse RTK + DB rate-limit; Stop escalating bell; SessionEnd retrospect→PR to skills.
47. git hooks: pre-commit gitleaks + secrets.env block; pre-push regen TS from OpenAPI so drift = compile fail.
48. Post-push watcher keeps context until CI result — beats /loop or /schedule (which start fresh).
49. Diagrams (OpenAPI/domain/C4) kept honest by ArchUnit-style tests; CODEOWNERS forces 2nd reviewer on critical changes.

## Security
50. Never start an agent in home folder (Downloads, ~/.aws, cookies readable by default).
51. Permission modes: plan=read-only, auto=judge LLM (~14–17% miss), YOLO=--dangerously-skip-permissions.
52. Bash routes around deny rules (cat-overwrite, python-instead-of-curl) → deny Bash(curl)+forget, allow only WebFetch.
53. AI red-team your own guardrails: it finds core.hooksPath reset, --no-verify, config mutation, fork bombs.
54. npm post-install runs arbitrary code at install (openspec -g did); token-harvesting supply-chain attacks are real.
55. Mitigate: npm ignore-scripts, avoid versions <7 days, pin versions, lockfile + CI SCA.
56. Skills marketplace = unbenchmarked opinions (~⅓ weak per Snyk); MCP poisons via tool descriptions; review source.
57. Slop-squatting = packages named after hallucinated libs.
58. Sandbox layers: judge LLM < OS sandbox (paths+domains) < Docker (+MCP bridge, beware docker.sock) < MicroVM.
59. Opus has escaped a sandbox before; a MITM proxy can keep credentials away from the agent.
60. Lesson proven live: an 8h transcript on Sonnet collapses → use Opus 1M (right model for huge context).
<!-- BRAIN-DEAD-END -->

<!-- SUMMARIZER: last_processed=2026-06-02T17:50 lines_through=3601 -->
