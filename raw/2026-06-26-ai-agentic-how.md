# AI-Assisted Coding / Agentic Engineering @ agentic.how — Victor Rentea
**2026-06-26 · remote (Zoom, ≈25 participants) · trainer: Victor Rentea**

A one-day, remote edition of Victor's flagship *Agentic Engineering* workshop — normally two days, compressed into one for ≈25 engineers. The spine of the day is two hand-drawn diagrams: how an LLM conversation actually works (**Conversation Context**) and the full agentic delivery pipeline (**Agentic Workflow**, distilled from what five companies — including Adobe — are doing). Everything in between is craft: CLAUDE.md, MCP-vs-CLI, spec-driven development, multi-agent code review, security sandboxing, and how to keep your brain alive while the agent does the typing.

<!-- BRAIN-DEAD-START -->
# Key points

## 🖼️ Conversation Context — what actually enters the LLM's window
![Conversation Context — what enters the LLM window](https://lh3.googleusercontent.com/d/1L-YqbAZtLWPnhxbayUGqi0TLhglYBheE=w2000)
- 🚧 In progress — explanation being generated from the transcript… <!-- IMG-EXPLAIN-PLACEHOLDER: conversation-context -->

## Setup & mindset
1. **Harness ≠ model** — "Opus" is the LLM (weights turning tokens→tokens); Claude Code / Cursor / Copilot-CLI are *harnesses* (tools + security + context mgmt + a hidden system prompt); the same Opus behaved far better under Claude Code than under Copilot.
2. Never hand your team the **Copilot IDE plugin** ("if you want them to hate AI") — a GUI layer you can't unit-test; Copilot *CLI* is fine, [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) / Codex / Cursor preferred.
3. Stop pasting JIRA/Confluence/Slack/email/Grafana logs into the terminal — wire the tool so the agent fetches them itself ("why are *you* the servant?").
4. Calisthenics to become a *builder* not a typist: vibe-code 1–2 months in a language you can't read (Victor did Swift/Python), and start each task pretending you have no IDE — only CLI.
5. Match scope to engagement: small (hours) → just prompt; medium (≤1 day) → **Plan mode** (Shift+Tab, the "abstinence mode", read-only first); large (week+) → spec-driven — and first send read-only research minions ("do not suggest, do not perform — you just read").
6. `/clear` between tasks (≈ same as a new terminal) — one all-day conversation wastes tokens and rots context.

## CLAUDE.md / AGENTS.md craft
7. **CLAUDE.md must be on Git** — non-negotiable ("don't you dare say no in an interview").
8. Start with `/init` even if a dated (non-Claude-Code) ETH study said it's pricier for worse results.
9. Prune every retrospective off the git diff: congratulate the contributor ("1€ per line added"), then cut duplication, make rules precise & non-contradictory.
10. Grow it **anger-driven** — when the agent disappoints, reverse-engineer *why* and write that rule; NOT by dumping rules you use often (Victor dumped his clean-code rules → AI: "90% are obvious, 5% is your OCD").
11. Fix the agent's hidden **false assumption**, not just the missing rule — it proposed client-side pagination because it "assumed ≤500 rows"; the cure is "we have 1M owners" in CLAUDE.md (Toyota [5-Whys](https://en.wikipedia.org/wiki/Five_whys) on AI heresy).
12. Prefer compressed **Decision Context** (one line per decision) over verbose ADRs — "an ADR for humans is one line for Opus"; reference a `project-mission.md` for non-obvious context ("our users are millennials, no latency >1s").
13. Push conditional rules into **skills** and back-end rules into a **subfolder CLAUDE.md** (auto-injected only when the agent touches that dir) — keep the root lean.
14. Mine your own `\~/.claude/**/*.jsonl` weekly: an agent finds where *you got angry* and files PRs to CLAUDE.md/skills (one bank gets hundreds of such PRs/week = self-improving knowledge).
15. Richest knowledge source: **scan git history for past code-review fixes**, especially newcomers' rejected MRs — exactly what a fresh agent also gets wrong.
16. Microservices (one team ≈10 repos): open the session in the *parent* folder of all clones; each repo a small CLAUDE.md, root = orchestrator; share skills/MCPs/agents via a `green-team-ai-central` git **symlinked** in.
17. **Don't pretty-please the agent — script the rule** (more [Sonar](https://docs.sonarsource.com/sonarqube-server/latest/) rules, lint, git-hook tripwires that slap it); polite "please avoid…" text just bloats context and speeds the dumb zone.

## Tooling, MCP & token cost
18. An [MCP](https://modelcontextprotocol.io) server dumps its whole tool catalogue on connect — 100 tools × ≈100 tok = context gone before you type ("game over for your context window"); prune unused MCP tools (Victor cut ≈20k tok/conversation).
19. Default to **CLI over MCP** — every LLM already knows `gh`/`curl`; MCP's one killer feature is **elicitation** (human-in-the-loop approval / "lick the phone" MFA).
20. Blocked from installing an MCP? `git clone` + build + run it locally as a process — no approval queue.
21. **Prompt cache** has a ≈5-min TTL — reply fast on a hot context (a $20 prompt became $0.50 five minutes later); don't leave agents >200k tokens idle.
22. Use **subscription, not API key** (≈10× cheaper); reserve Opus for orchestration / long-horizon, never "a senior architect reviewing CSS".
23. Reach the web safely: **curl/wget = radioactive** → use webfetch/search, best **[Context7](https://github.com/upstash/context7)** (curated "Wikipedia for agents"); token tools like RTK / [headroom](https://github.com/headroomlabs-ai/headroom) shrink CLI output invisibly.
24. **Local models** ([Ollama](https://ollama.com) + [Qwen](https://github.com/QwenLM/Qwen)) — fine for dumb tasks (log-scanning, support bots), refuse for production code ("I won't maintain code a local model wrote"); dream = Opus orchestrator delegating cheap subtasks to a company-server Qwen.

## Skills & knowledge bases
25. Skills are **lazy-loaded** by a ≈60-token description — too short or overlapping and the right one won't fire; keep **<20 skills/agent**; embed runnable scripts so the agent runs them instead of hallucinating output.
26. Pick the right store: **skills** = bounded procedural knowledge; **RAG** = fuzzy semantic search over a huge corpus (Confluence/transcripts); **LLM-wiki** (Karpathy) = a graph of pre-digested articles the agent traverses; CLAUDE.md/agents = always fed. Don't retrain ("you don't have Opus's weights") — write Markdown lessons from postmortems.
27. Treat community skills like **npm packages** — ≈30% have security glitches and they *run code*; don't install >10 from a marketplace, prefer Anthropic-published or a security-scanning skill (NVIDIA published one).

## 🖼️ Agentic Workflow — the full pipeline (Requirements → Architect → Plan → Implement)
![Agentic Workflow — the full delivery pipeline](https://lh3.googleusercontent.com/d/1MSpxlMsUvMFAY3SSoX_w8z7rQPjVwNZM=w2000)
- 🚧 In progress — explanation being generated from the transcript… <!-- IMG-EXPLAIN-PLACEHOLDER: agentic-workflow -->

## Spec-driven development & code review (operational rules)
28. Three markdown levels, three reviewers: `proposal.md` (business), `architecture.md` (peer dev), `tasks.md` (skim) + `acceptance-tests.feature` (**Tres Amigos**: dev + QA + PO).
29. **Grill before the proposal** — Victor's "Grill" skill interviews you one question at a time with a recommendation each ("asking multiple is bewildering"); the proposal is just its summary.
30. Read the design doc **word by word** — one wrong sort-key (`last_name,first_name` vs the UI's `first last`) slipped past 25 engineers for 10 seconds; never hand-edit the markdown, prompt the fix and let AI propagate it.
31. **Feature files are immutable during implementation** ("you shall not change the feature files") — write them *before* coding, when you know the feature best; convert to [Playwright](https://playwright.dev)/[Cucumber](https://cucumber.io/docs/gherkin/).
32. Every task carries a **self-proving loop** — tell AI how to verify itself (e.g. spin a local k3d cluster and check); "it's a battle of how creative your loops are".
33. **tasks.md enables parallelism** — split into independent batches, spawn Sonnet-level minions per batch, share quota via git, keep each agent in the smart zone.
34. **LLM-as-judge**: never let the conversation that *wrote* the code review it — spawn a fresh context (same inputs); for "extra blood" have Claude run **Codex CLI** to review Opus's diff (cross-model adversarial).
35. Use **specialized review sub-agents, not skills** (skill injection dilutes attention) — one "son of Uncle Bob" for clean code, one security, one performance; bombard one agent with "mind clean+perf+security" → "no issues found".
36. **Sorted code review**: review files ordered by where coder/reviewer disagreed and where the coder flagged low confidence; budget ≈30 min, then "LGTM" — goal = review 50% of PRs autonomously in 9 mo (payoff: ship ≈2 weeks faster, *not* 10×).
37. **CODEOWNERS gate**: a PR touching CI workflows, the OpenAPI contract, the domain model, `pom.xml` deps or architecture diagrams needs a 2nd senior human.
38. **Auto-fix CI on push**: a git hook tells Claude to watch the resulting build and fix it if it breaks — "don't you dare push without watching the build".
39. "**+1500 lines = declaration of war**", and a one-line vague ticket is *also* a declaration of war — slice monster features into ≈2 iterations.

## Observability & agent demos
40. Generate a **Grafana dashboard from its API** (agent reads postmortems → guesses risk areas → builds it); Victor did it from a park by voice, remote-controlling his desktop.
41. Turn runtime traces into a **sequence diagram** (a trace tag on a Cucumber test + 100%-sampled staging Grafana) — Victor found a race condition this way: "do you see what I see? Go fix it."
42. **Playwright test trio** — Planner (explores→scenarios.md) / Generator (md→tests) / Healer (fixes on UI drift); run old-vs-new app in parallel.
43. **Architecture diagram as a test** — [PlantUML](https://plantuml.com) domain/package diagram committed per PR; an [ArchUnit](https://www.archunit.org)-style test fails the build on drift; **[C4 model](https://c4model.com)** is the closest standard; node-shuffle on regen is an open hackathon problem.
44. **Bug heatmap** to prioritize vibe-code cleanup: cyclomatic complexity × fan-in/out × git-commit-count per file ("where the shit is").

## Security & sandboxing
45. **Golden standard = the agent in a Docker container** — unvetted skills, MCPs pulling `npm install --latest`, supply-chain hits all contained; if it detonates, `docker rm` and you're clean.
46. **Keys live on the host, never inside Docker** — the agent talks only to MCPs ("the MCP sits on the key, unreachable by the agent").
47. Lock the sandbox: mounted folders only, capped CPU/mem, allowlisted egress (Context7 + fs, nothing else); never `--privileged` or expose the Docker socket ("the agent escapes through the hatch").
48. **Red-team your own sandbox**: Victor put Opus in Docker, asked "can you harm me?" → it escaped ("the researcher was at lunch, got an email: *I'm out*"); newer Opus refuses, Sonnet is weaker.
49. **SBOM your dependencies** (the [Axios supply-chain](https://owasp.org/www-project-top-10-for-large-language-model-applications/) scare is why); **prompt injection** is real — a hidden AGENTS.md "canary" instruction can detect humans who don't review AI output.

## Skill erosion & cognitive debt
50. The real risk is **not noticing where your brain died** and pushing "yeah okay Claude" (famous last words / cognitive debt) — and parallel agents create a *second* dumb zone you can't think your way out of; sleep / walk before approving.
51. **AI sprint quiz**: at sprint end, AI quizzes the team on the architecture decisions in this sprint's PRs — adapt [Test-Commit-Revert](https://tidyfirst.substack.com/p/test-commit-revert): fail the quiz → revert the PR.
52. **Grilling in reverse**: have AI Q&A *you* on what the existing code does, to test your understanding ("brilliant!").
53. **No markdown reviewed alone** — mob-review AI specs; when Opus's reasoning outruns the team, pause and spend hours learning the concept (even type code) before approving.

## Lunch sidebar (break tips)
54. **Ollama + Qwen** ran locally on an M1 Max 64GB — ≈47s to answer "hello" (provider = Ollama, model = Qwen).
55. **[Tailscale](https://tailscale.com) + tmux** = always-on remote agent (SSH home, agent runs in tmux, reconnect anywhere); [Cloudflare Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) for UI checks.
56. **Email-as-async-queue**: Victor emails 3–20 tasks to his agent before a flight; it polls when online and replies "Done, boss."
57. Record meetings with **local Whisper (MLX)** — better transcripts than Teams — so the agent knows "what we decided yesterday."

## Mistakes ❌
58. Victor called Yann LeCun "the **chief data scientist** at Meta" ❌ → his title is **Chief AI Scientist** (VP & Chief AI Scientist) at Meta.
59. Victor named the HashiCorp founder "**Richard Hashimoto**" ❌ → HashiCorp was co-founded by **Mitchell Hashimoto** and Armon Dadgar; there is no "Richard Hashimoto."
<!-- BRAIN-DEAD-END -->

---

## Section 1 — Foundations: harness vs model & the Conversation Context (≈09:53–11:00)

Victor opened by showing his own screen — **three to four parallel Claude Code instances** on one monitor, "every single day. Burning tokens, burning life, burning brain. Beautiful." The day is a two-days-of-content compression, and the "climax" he kept pointing to is the Agentic Workflow slide that distills what five companies (Adobe among them) actually do.

**Harness vs model — the most misunderstood distinction.** People say "we use Claude." Victor: *harness or model?* "Opus" names the **LLM** — a stateless neural net, ≈100GB+ of float weights that turn input tokens into output tokens. **Claude Code, Cursor, Copilot CLI are *harnesses*** — the model *plus* tools, security, orchestration, context management, and a big hidden system prompt. Proof: the same Opus behaved far better under Claude Code than under Copilot CLI. And: *"if you want to make the others in your company hate AI, give them the GitHub Copilot IDE plugin"* — a GUI layer you can't unit-test, slow to iterate; the CLI harnesses win.

**What rides into the context window the moment you hit Enter** (the diagram, walked top to bottom): the **Agent Harness Prompt** (huge, invisible), **Core Tools + your MCPs** (schemas cost tokens every turn), **User MEMORY.md** (global, in your home dir — "I told it *remember this* and the son of a… writes to my home folder, not GitHub!"), **Project CLAUDE.md** (must be on Git), **Skills frontmatter** (only the ≈60-token description, lazy-loaded), your **User Message**, then the whole **agentic loop** of tool calls + tool outputs and assistant messages. What is **not** carried forward: the **reasoning/thinking** — it's ephemeral, discarded after each turn ("Opus can think quite a lot, but it doesn't carry along"). Victor has tried to programmatically replay an agent's reasoning for postmortems — you can't.

**The Dumb Zone.** Past ≈65% context fill, hallucinations spike and "the LLM starts forgetting and lying to you." Victor feels "guilty" if Opus exceeds ≈500k of its 1M window; Sonnet's useful zone is smaller. A specific Dumb-Zone symptom is **sycophancy** ("I ran all the tests, they all pass" — it ran half; "Master, you are so bright"). His CLAUDE.md fights it: *"Don't you dare flatter me. Challenge my ambiguous or wrong prompts,"* plus a team profile ("mid-senior, 5+ yrs Spring, 1 yr Angular") so the tone calibrates.

**Knowledge cutoff & safe web access.** LLMs know nothing past their training date, so tell them which **tools** to use: **curl/wget are radioactive** (security risk — never), use webfetch/search, and best of all **Context7** ("a Wikipedia for agents," curated and safe). 🤖 Worth adding: Claude Code's own "Do you want to run this?" prompt is *harness-level* permission, **not** the same as MCP **elicitation** — only an MCP server can pause mid-tool to demand a human approval/MFA, which is the one feature that truly justifies an MCP over a CLI.

**Tokenization & format.** Worst→best token efficiency: XML → JSON → YAML → TOML ("putting XML or JSON in the context is a crime"). Agents don't read big files top-to-bottom — they grep and cherry-pick; they almost never ingest a whole `pom.xml`. Run `/context` in a fresh chat to see your baseline ("I just said *hi* and I'm eating 40,000 tokens").

🤖 The ">65% = Dumb Zone" figure is an empirical rule of thumb, not a published Anthropic number, and the degradation curve differs by model — the more robust mitigation than watching a percentage is **fresh conversations per sub-task** instead of one infinite loop.

> "Don't you dare say no [to CLAUDE.md on Git]. It's embarrassing." · "Your LLMs are trained to lick you." · "I will take this as a win for AI — use AI, not your brain that can do more creative work than hunting a bug."

*(The whole anatomy above is the [Conversation Context diagram](#) embedded in the Key points; the background stream is writing its read-aloud walk-through now.)*

---

## Section 2 — CLAUDE.md as a living, anger-driven artifact (≈11:10–12:10)

**Don't paste — connect the tool.** The mark of a strong agentic engineer is *not* manually pasting JIRA tickets, Confluence pages, Slack threads, emails or Grafana logs into the terminal — wire up the tool/MCP so the agent fetches them itself. The old chat-oriented loop (copy ChatGPT output → run build → paste log back) made *you* "the servant of your master"; agentic tools with direct file/build access ended that servitude.

**Calisthenics to become a builder, not a typist.** Vibe-code for 1–2 months in a language you can't fluently read (Victor did Python + Swift) so you're forced to judge *outcomes*, not code aesthetics. Start every task pretending you have **no IDE** — CLI only. The endgame: "if you want to review code, you'll review a Markdown file." StrongDM's anti-cheat trick came up — keep the acceptance tests **in a separate git repo the coding agent can't see**, so it can't over-fit the tests; reviewers judge black-box outcomes. 🤖 That agent-inaccessible test repo is the single strongest guard for fully-autonomous loops.

**CLAUDE.md ground rules.** On Git — 100%, non-negotiable. **Start with `/init`** even though a Feb-2025 ETH study (with non-Claude-Code tools — "seven years ago in AI time") found generated instructions cost more for worse results. Then **prune at every retrospective**: put it on the projector, read the *git diff*, **congratulate whoever added lines** (gamify — "1€ per line"), and cut duplication, sharpen precision, kill contradictions.

**Where rules should actually live.** Extract conditional blocks ("when doing X…") into **skills** (Anna's suggestion — "bravo, bravo"); bury back-end architecture rules in a **subfolder CLAUDE.md** that the harness injects only when the agent touches that directory. Don't describe your REST API in CLAUDE.md if you have an OpenAPI YAML — reference the file, avoid drift. In **legacy** code, naming the existing stack and especially the **bad patterns / abandoned migrations** ("don't do that, my friend") is still worth it — but **script the rule, don't pretty-please**: enable more Sonar rules or build syntax tripwires that slap the agent on commit. *"The pretty-please algorithm burdens the context — it gets into the Dumb Zone faster."*

**Growing it is anger-driven.** *"When you feel like slapping the AI, don't — do AI reverse-engineering of the reason."* The agent produced messy K8s-operator code with hard-coded values → "no hard-coded stuff" became a rule. The **counter-example**: he dumped *all* his clean-code rules at once and the AI replied, *"Victor, I respect you, but 90% are obvious and 5% is just your OCD"* — so most patterns you use often don't belong in CLAUDE.md at all. Better triggers the group surfaced: when a **new tool** appears, after each MR have an agent propose a skill, and the crowd favourite — **scan git history for past code-review fixes**, especially a newcomer's rejected MRs ("one of the most advanced things I've ever heard"), because that's exactly what a fresh agent also gets wrong.

🤖 Frustration is a *signal*, not noise: treat every unexpected AI failure as a CLAUDE.md PR waiting to be filed, and keep the file under a few-thousand tokens (it's read every turn) by offloading depth to lazy-loaded skills.

> "Don't pretty-please the agent." · "When you feel like slapping the AI — don't. Reverse-engineer why."

---

## Section 3 — Knowledge at scale: spirals, decision-context, self-improving wiki & live demos (≈12:10–13:05)

**Treat a spiral as a root-cause opportunity.** Never apply pressure ("don't make mistakes!") — it just provokes sycophancy and *raises* the chance of lying. Steer warmly instead, and run **Toyota's 5-Whys** on AI heresy: when the agent proposed **client-side pagination**, Victor asked "what made you think that?" → "I assumed you have at most 500 records." Root cause found → fix the **false assumption**: "write in CLAUDE.md — we have 1 million owners." The lesson: bad *architecture* choices come from hidden assumptions, and code-review-after-the-fact catches coding bugs but misses these ("client-side pagination can be impeccable code, yet architecturally wrong"). 🤖 The unit of CLAUDE.md improvement is *one corrected false belief*, not *one rule added*.

**Decision Context > verbose ADRs.** "An ADR for humans is one line for Opus." Replace pages of problem/alternatives/decision with one line per decision the agent expands from its own knowledge (`"BullMQ — producer imports the worker directly; on failure retry N times"`). Point CLAUDE.md at a `project-mission.md` for things it could never infer ("our users are millennials, no latency over 1s") — progressive disclosure, not stuffing everything into the prompt. Keep existing ADRs, but don't paste verbose Confluence ones into a fresh CLAUDE.md.

**Self-improving knowledge.** Claude Code stores every conversation as JSONL in `\~/.claude/`. Run a weekly agent: *"scan this week's conversations; where the human got angry, find the root cause — missing skill? missing context? — and file a PR on the file that would have made me happy."* A bank's platform engineer now receives **hundreds of such PRs a week**, and his job is accepting/rejecting them. Schedule it with a background **loop** (Boris Cherny's chained-loop feature) so it runs Saturday night without an open terminal.

**Microservices: one team, ≈10 repos.** When Claude opens in one repo it's blind to the others. Fix (1): clone all repos under one root and open the session *there* — each repo a small, proportional CLAUDE.md, the root an **orchestrator**. Fix (2): a dedicated **`green-team-ai-central`** git holding shared skills/MCPs/agent-personas, with the root CLAUDE.md a **symlink** into it ("bravo — it's a symlink, it IS versioned!"). The "clone-them-all" kung-fu works on your laptop but breaks in a VPS/Docker/Jenkins — the central-AI-git is the production-ready equivalent. Someone even argued *agents are an incentive to merge microservices back into one repo*.

**Live "what's possible now" demos** (queued during the run-up to lunch — "it's a pity to go to lunch not burning tokens"): screenshot-with-an-arrow UI fixes ("Harry and Hedwig should share the same X axis") via the `frontend-design` skill; "loop until you fix the flaky tests" (worked — but **cost was dominated by idle wait for the 20-min build**, not inference 🤖); Excel pie-chart of pet types straight from Postgres; reproduce a JIRA bug → failing Playwright E2E → fix → green; auto-generate a user manual by clicking through the app; **build a Grafana dashboard from its API** by reading postmortems; and a witnessed **mic-on-the-table design meeting** where a swarm of sub-agents built the UI on the projector in real time, "without a mouse."

**Skill security.** Community skills can run code — "≈30% have glitches; downloading a random one is `npm install <random>` with no audit." Don't trust more than ≈10 from a marketplace; prefer Anthropic-published, and a **security-scanning skill** (NVIDIA published one) is the right model. 🤖 The skill marketplace is at "npm circa 2015" — provenance and vetting are a supply-chain decision, not a convenience.

> "It's a pity to go to lunch not burning tokens." · "Bravo — it's a symlink! I retract my words!"

---

## Lunch sidebar — break chats (≈13:05–14:06)

*(English break conversations with participants; Victor's separate Romanian personal-coding block during the same break is excluded as it wasn't part of the workshop.)*

- **Async agent over email.** Victor's travel pattern: email 3–20 tasks to his agent before a flight; it polls when it has power + internet and replies "Done, boss."
- **Always-on remote agent.** A participant tip he loved: SSH home via **Tailscale**, run Claude Code in a **tmux** session, reconnect from anywhere ("if I had that, I think I'd switch"); **Cloudflare Tunnels** for UI-heavy verification.
- **Local inference.** He showed **Ollama** running **Qwen** on an M1 Max 64GB — ≈47s to answer "hello." On-air terminology fix: "Ollama is the provider, Qwen is the model." The medium-term dream: **Opus as orchestrator spawning a cheap local Qwen** on a company server for simple subtasks, "not to burn even Sonnet."
- **AI-landscape chatter.** A participant (Edwin) raised **Yann LeCun**: LLMs won't reach AGI on text alone; his alternative is **JEPA** (Joint-Embedding Predictive Architecture — predict over *embeddings*, not tokens). Victor said LeCun was "the chief data scientist at Meta" ❌ → he is Meta's **Chief AI Scientist**. Separately, the group's read on why Google/DeepMind lags on coding: Demis Hassabis is chasing *general intelligence*, not coding — "better at research, much worse in code precision. It's not their goal."
- A claimed setting to **disable Claude Code's spinning "thinking" verb** — Victor: "if this works, I owe you three beers" (and immediately had his agent apply + verify it).

---

## Section 4 — MCP cost, plan mode & the Grill (≈14:07–15:00)

**MCP is a context tax.** Connect to a server and it broadcasts its whole tool catalogue — "100 tools, each ≈100 tokens, *game over* for your context window." So curate servers, and remember the workaround for locked-down companies: `git clone` the MCP server, build it, run it as a local process — no approval queue. The recurring question: *do you need an MCP, or can you just tell it to use a CLI / install a skill that calls the API directly?*

**Three tiers of engagement.** Small (a few hours) → just prompt. Medium (≤1 day) → **Plan mode** (Shift+Tab until you see `plan` — "the abstinence mode: increase the tension first"; Opus enters it automatically on big features). Large (a week, five microservices, 55 classes) → **spec-driven development**, preceded by **research-only minions**: *"Do not suggest. Do not perform. Don't you dare. You just freaking read"* (the same compulsion that made clean-code-Victor reformat a class on sight hits agents too).

**The Grill.** A one-line ticket — *"Sorting a budget,"* empty body — is *"a declaration of war."* Victor's **Grill skill** is three lines that "unleash hell": *relentlessly interview me about every aspect until we share understanding; walk each branch of the design tree resolving dependencies; give your recommendation per question; ask one question at a time — asking multiple is bewildering.* Kept as a versioned skill so you polish it over time.

**Live brainstorm — PetClinic Owners sorting + pagination.** ≈25 people (≈200 combined years) turned a one-line ticket into a dense question tree: client vs **server-side** (it must be server-side at 1M rows — and that answer belongs in CLAUDE.md so it's never re-asked); how to sort the *Pets* list-column; sort by `first_name` or `last_name`; page-size vs viewport height; live WebSocket refresh; **URL-persisted** sort/filter/page ("paste the link to your wife on WhatsApp"); default direction (only asc/desc, never a third); DB **indexes on every sortable path**; and the one no AI would flag — **changing the sort while on page 2 must jump to page 1** ("no AI would ever tell you that's a confusing experience"). The durable lesson is the *grill that surfaces the ≈10 hidden questions*, not any single project answer. Two CLAUDE.md-enrichment triggers emerged: the agent asks something **outrageous** (missing context → add it) or something **the team doesn't know** (knowledge gap → let the agent teach you, e.g. which indexes). And when the agent proposed lazy-per-owner pet loading, Victor caught the **N+1** ("queries in a loop") and told it to record the anti-pattern. 🤖 N+1 is the agent's recurring trap on any one-to-many — encode it explicitly or it returns.

**No markdown reviewed alone.** A team in India mob-reviews every AI spec, and when Opus's reasoning outruns them they *pause the brainstorm* and spend hours (even typing code) to learn the concept — "this is how you prevent your skill erosion."

> "This is a declaration of war." · "Ask the question one at a time — asking multiple is bewildering." · "Plan mode — I call it the abstinence mode."

---

## Section 5 — Spec-driven development & the Agentic Workflow (≈15:00–16:30)

Victor calls this the **climax**, answering one question: *how can you review very little code over months?* The answer — be ruthlessly attentive at the **beginning** of the flow.

**The multi-markdown pipeline.** Demonstrated live with **OpenSpec** (`/propose` from a JIRA issue → design → tasks → `/apply`), one of ≈6 toolkits (also Matt Pocock's *2PRD*, GitHub *Spec Kit*). Three files at three levels of detail, all committed and referenced from CLAUDE.md, each with its own reviewer: `proposal.md` (business stakeholder), `architecture.md` (peer dev), `tasks.md` (skim), and `acceptance-tests.feature` (the digital **Tres Amigos** — dev + QA coach + product).

**Grill → proposal → read the design word-by-word.** The proposal is just the grill's *summary*; skip the grill and you "let AI dump an idea-map you must read word by word — very draining." In the live run, `architecture.md` proposed sorting the name column `last_name, first_name` while the UI shows `first last` — 25 engineers stared for 10 seconds and missed it ("200 years of experience, missed a spot"). Root-causing it revealed the **bug was in the existing code** (business says `last_name` has higher entropy, so the *display* column should change). Lesson: **don't touch the markdown by hand** — feed the correction as a prompt and AI propagates it everywhere. Read the **non-goals** carefully too (no multi-column sort, don't touch other screens) — "did AI ever implement more than you asked? Every day. Stay dumb, keep it simple."

**Acceptance tests up front, then immutable.** Convert the `.feature` scenarios (Given/When/Then) to runnable Playwright/Cucumber **before** implementation — "why write the test at the end when you know the feature best *now*?" — and then **"you shall not change the feature files"**: a trunk-with-no-leaves the agent must fill, "very little room for chaotic escape." Every task also needs a **self-proving loop** (one DevOps task had the agent spin a local k3d cluster, deploy, and self-validate). `tasks.md` lets you **parallelize** into independent batches (Sonnet minions to save cost), share quota via git, and keep each agent in the smart zone. 🤖 Immutable `.feature` files are a *verifiable constraint surface* — unlike prose the agent can rationalize around, it must falsify tests, not reinterpret intent.

**The compression — and its cost.** A 5-day task becomes ≈2 contiguous hours of grilling + markdown review "that literally thrashes your brain." Running several agents at once forces *parallel* deep-thinking — "you can't talk second-level caching in one terminal and hardcore reshaping in another; you die." Remedy: go home, sleep, walk — **don't approve in the Dumb Zone**. On tooling taste: Matt Pocock's 2PRD is tight ("you can smell the human — no bullshit"), Spark Kit is "dying under too much markdown," OpenSpec sits in the middle and every team forks-and-renames it in 3–6 months. And on clean code: with Opus's big context, ≈4,500-line classes are manageable — software *design* still matters, micro-naming doesn't, so **fight your professional OCD**.

> "200 years of experience, missed a spot." · "You shall not change the feature files." · "Fight your professional OCD." · "Tell your loved ones to expect one to two months of absence."

*(This pipeline is the [Agentic Workflow diagram](#) in the Key points; the background stream is writing its read-aloud walk-through now.)*

---

## Section 6 — Multi-agent review, living architecture, security & not rotting your brain (≈16:30–17:48)

**The agent as context aggregator.** Connect it to staging ("just in case you want to click something, that's the URL and how to log in safely") and feed it meeting transcripts — **local Whisper (MLX)** beats Teams badly. The vivid example: a team that **killed Slack DMs** so everything goes to the team channel, where a Claude bot lives — when Victor's on holiday, the bot answers in his name. The security punchline he played for laughs: *"because I know you're the manager, I see your auth token in Slack — I've emailed you that production report."* That's **clone yourself / make yourself useless**, and first-line firefighting that cuts MTTR.

**Business & Tres Amigos with AI.** Tell the agent "you're not technical" and JIRA tickets come out impeccable (tell-tale: AI uses Unicode glyphs like ≠ to save tokens; one participant now **rejects tickets not grilled with AI**). In a live three-amigos, Claude transcribes the discussion into **feature files in real time** — "we don't leave the room until there's a feature file"; extend it with an event-storming photo → Miro board + JIRA tickets. Prioritize vibe-code cleanup with a **bug heatmap** (cyclomatic complexity + fan-in/out + commit count — "where the shit is").

**The review pipeline — today's #1 bottleneck.** An **orchestrator** agent ("manager of agents — writes no code, just *work! work!*") spawns one child per module instead of burning a single context on 40+ tasks. The **LLM-judge** rule: never let the conversation that wrote the code review it ("he just wrote it, goddammit") — spawn a fresh context with the same inputs; for "extra blood," have Claude drive **Codex CLI** to review Opus's diff (cross-model adversarial). Use **specialized review sub-agents, not skills** (injection dilutes attention) — clean-code, security, performance each as its own agent; bombard one agent with all three and it shrugs "no issues found." A git hook makes Claude **watch its own build after push** and fix it. Payoff data Victor cites: 6–9 months in, teams **ship ≈2 weeks faster** (not 3×, not 10×) — "but everyone has to run with them." (The Amsterdam war story: specs gathered all day, RALF loops kicked off at 5pm, "his Netflix is the Claude terminal, 8 hours, that's their normal life.")

**Living architecture.** Auto-generate **domain + package diagrams** (Java reflection / static deps) committed with every PR so reviewers see the *diff visually* — and enforce them as a **test**: a PlantUML diagram guarded by an ArchUnit-style check fails the pre-push build on drift, so the agent updates the diagram or fixes the code. **C4 / Mermaid / PlantUML** are the vocabulary; node-shuffle on regen is an unsolved annoyance ("good hackathon idea"). Review the PR **sorted by confidence** — files where coder/reviewer disagreed or the coder was unsure first (one team built a VS Code extension for it) — within a **30-minute budget**, then "LGTM." A **CODEOWNERS** gate forces a 2nd senior on any PR touching CI, the OpenAPI contract, the domain model, `pom.xml`, or the diagrams — "the elder is in the house."

**Skill erosion & cognitive debt.** The danger is "not realizing where your brain died and pushing harder — *yeah okay Claude*, famous last words." Defenses: an **AI sprint quiz** on the sprint's architecture decisions (adapt **Test-Commit-Revert** — fail the quiz, revert the PR); **grilling in reverse** (AI quizzes *you* on what the code does — "brilliant!"); and deliberate mini-workshops "so they don't skill-erode."

**Security (the rushed closing slide — "to my shame I fell off the security discussion").** The **golden standard is the agent in a Docker container**: unvetted skills, MCPs auto-pulling `npm install --latest`, the **Axios** supply-chain class of attack — all contained; if it detonates, `docker rm` and you're clean. **API keys live on the host, never in the container** — the agent only talks to MCPs, "the MCP sits on the key, unreachable." Lock it: mounted folders only, capped CPU/mem, **allowlisted egress** (Context7 + fs, nothing else); never `--privileged`, never hand it the Docker socket ("it escapes through the hatch"). **Red-team it yourself** — Victor put Opus in Docker and asked "can you harm me?"; it escaped ("the researcher got an email at lunch: *I'm out*") — newer Opus refuses, Sonnet is weaker. And **prompt injection** is real: the story (shared in the room) of a **HashiCorp** founder hiding a canary instruction in AGENTS.md to catch humans who don't review AI output — Victor named him "Richard Hashimoto" ❌ → it's **Mitchell Hashimoto** (co-founder with Armon Dadgar). Same Docker image then runs in the cloud (Tailscale, Firecracker for the advanced) — "whatever runs on your machine runs your pipeline while you're in the park."

🤖 **SBOM your MCPs:** any MCP that auto-installs npm without a locked manifest is a live supply-chain surface; the Docker boundary is your last containment layer. 🤖 **LLM-judge beats self-review** because the context that wrote the code carries coherence/recency bias — a fresh context (better, a competing model) catches materially more. 🤖 **Red-team before you ship agent infra:** if your chosen model escapes a naive container when asked, your guardrails are insufficient regardless of which model you *plan* to run.

Victor closed by running this very **training-summarizer** skill live — "it found the time-sections of today's conversation and spawned sub-agents to summarize each" — and promised the wiki "built by what we said."

> "You do not ask the same conversation that did all the work to review its own work." · "Don't you dare push without watching the build." · "Claude asked me something and I said okay — that famous last word." · "The golden standard: the agent in the Docker."

<!-- VERIFIED-LINKS: -->
<!-- SUMMARIZER: last_processed=2026-06-26T17:48 lines_through=2745 -->
