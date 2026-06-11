# AI Agentic Engineering @ Playtika — Day 1 (2026-06-11)

> Workshop language: **English**. Day 1 of a 2-day session. The whole day ran live on the [pet clinic](https://github.com/victorrentea/petclinic) project (Spring Boot + Angular), inside the terminal.
> Day 2 (security, Docker sandboxing, prompt injection, OpenClaw, the full multi-agent review workflow, the OpenAPI→TypeScript drift pipeline) was repeatedly previewed but **not yet covered** — those land in the day-2 delta.

---

## 1. What actually goes into the LLM, and the token economy (≈09:30–10:30)

Victor opened by previewing the two days: spec-driven development with human-in-the-loop, a code-review wiki, **quality guardrails + tripwires** ("tell the AI its code is bad *just in time* for it to fix — not too early, not too late"), cross-repo knowledge sharing, and a security-heavy day 2.

**The first exercise — what travels with your message?** When you open a fresh chat, type one sentence and hit enter, far more than your text reaches the model:
- **System / harness prompt** — "you shall not do harm", baked in by the harness.
- **Context** — your message (+ the files it references).
- **Tools** — the harness's built-in `edit`/`write`/`glob` **plus** your MCP tools.
- **Memory** — `memory.md` in your home + the project `CLAUDE.md` in the current working directory.
- **Skills front-matter** — the *description* of every installed skill is eager-loaded so the agent knows what it *could* load on demand.

Run `/context` to see it — Victor's fresh conversation already burned **≈20,000 tokens** before he typed anything (MCP + skills + RTK). 🤖 Do `/context` at the start of a sprint: a bloated baseline (too many always-on MCP servers) silently shrinks your usable window and pushes you toward the dumb zone faster.

**Harness ≠ model.** The *model* is the LLM (Opus, GPT, DeepSeek). The **harness** is everything around it — defined later in the day as *a human interface + a set of tools that give the LLM **knowledge** (CLAUDE.md, skills, wiki, graph) and **actions** (run builds, deploy, git, restart prod)*. Harnesses: Claude Code, Cursor, Codex, OpenCode, and **Glean** (an in-house "ChatGPT" you type *into*; plug in codebases, Slack, Jira; MCP-capable).

**DeepSeek + sovereignty.** A DeepSeek coding model claimed near-Sonnet quality at ≈10% of the cost, with **open weights** you can run on-prem on a ≈half-million-dollar GPU box — "we don't depend on Trump." You can even point the same harness at it via [claude-code-router](https://github.com/musistudio/claude-code-router) (same skills, same hooks). 🤖 Treat "it *is* Sonnet on every benchmark" as marketing-flavoured: open models are genuinely strong now but the gap is task-dependent, and routing a prompt to a hosted DeepSeek API means your code leaves your perimeter — the on-prem story is the interesting one for a studio.

**Talk to AI in English — mostly.** ≈3% cheaper tokens and slightly higher quality. But the deeper point: *you* are the bottleneck. If a problem sits at the edge of your expertise, don't throttle your own bandwidth — think in your native language. And **stop typing**: dictate. Victor lives on [Wispr Flow](https://wisprflow.ai) (switches RO↔EN, knows jargon) and "vibe-coded" a whole project from a mall using voice + remote control.

**Tokenization & cost.** Your text is tokenized; you pay for input, and **output is ≈5× more expensive than input** (≈$3/M in for Sonnet-class). `git status` raw = 97 tokens.
- **[RTK](https://github.com/rtk-ai/rtk)** (Rust Token Killer) — a bash-command proxy that runs the command, polishes the output, and hands the model only what matters: that `git status` dropped 97→19 tokens. Their "80% savings" is marketing; **expect ≈30%**. Auto-configures via a hook so every `git status` quietly becomes `rtk git status`.
- **[TOON](https://github.com/toon-format/toon)** (Token-Oriented Object Notation) — a CSV-for-AI data format; the sample JSON went 86→38 tokens. Shines for **collections** (it doesn't repeat field names). A participant's sharp objection: *how do you know the model parses TOON as well as JSON, and doesn't burn more reasoning?* Victor's honest answer: **that's a job for your AI researcher** — every studio should have one person in a "white coat" who benchmarks (run the same task 50×, look at the mean and standard deviation). 🤖 Real risk: a format the model saw rarely in training can cost *more* output/reasoning tokens than it saves on input — measure end-to-end token cost, not just the payload size.

**Autonomy scales with the engineer.** "The stronger the agentic engineer, the longer the flows you can run." Longest single autonomous run in the room: **6 hours** — the agent fixed a pipeline (ran the build, waited 40 min, saw it fail, fixed, pushed). Weekend-long runs aren't one loop — they're the **[RALPH loop](https://ghuntley.com/ralph/)** (a bash `while` loop re-feeding the prompt). Dangerous: it implies `--dangerously-skip-permissions`, so **lock the agent in a Docker** (don't hand it the Docker socket). "If there's a bomb, it's contained in the door." Adobe runs agents this way on the pipeline.

**Anti-pattern — Caveman.** A tool that *decompresses* the model's terse output into long explanations. Victor hates it: it loads your **mental bandwidth** — the real last bottleneck. Just tell the model "keep explanations concise, I know what I'm doing." (He even joked that omitting the trailing period saves a token. It doesn't matter — one input token is noise.) 🤖 The thing worth protecting isn't tokens here, it's *your attention*: verbose AI prose is a tax you pay with your own focus.

Victor also told the **April-1 "Claude Code source leak"** story — feature flags that supposedly unlock more power once the harness decides you're a real developer (you understand JSON) vs a vibe-coder. 🤖 Treat this as folklore (the date is a hint); the durable point is real — models *do* adapt verbosity/assistance to the perceived expertise in your prompts.

---

## 2. Conversation state, prompt cache & plan-mode discipline (≈10:15–10:32)

**A conversation is a stateless API.** Every turn re-sends *everything before you* as input tokens, prefixed to your prompt. **Thinking is per-turn and thrown away** (not re-sent); **tool calls and their outputs accumulate** forever. So one tiny new token on top of a 65k-token history still pays for the whole prefix.

**The savior — and the trap — is the [prompt cache](https://platform.claude.com/docs/en/build-with-claude/prompt-caching).** The prefix you already sent is cached; you pay ≈**10%** for the cached part. But the cache **expires in 5 minutes** (it was 1 hour until ≈April, then silently dropped — people saw 5× burn). So if your agent asks a question and you reply >5 min later, you re-pay full price. "You're not a 10× developer, you're a 10× sucker." The classic failure: **four terminals open**, two with 500k accumulated context, neglected past the cache window → boom. 🤖 Cache *writes* cost ≈1.25× base (and the opt-in 1-hour cache ≈2×), so caching only wins when you actually reuse the prefix soon — idle terminals are the worst case both for cache and for the dumb zone.

**Plan mode = single-threaded deep thinking.** Don't open a *second* terminal while in plan mode: humans cannot run two deep-thinking tasks in parallel. (Napoleon could famously do three things at once on his horse — none of them deep thinking.) One terminal does the hard thinking; the others do shallow polishing, at most.

> Aside: `codex-gpt` / Claude Code on an OpenAI key works, but front-matter, hooks and CC-specific workflow features may not port — orchestration tools are usually built around the models they ship with.

---

## 3. Why AI hallucinates, and the Dumb Zone (≈10:27–10:42)

**Why the model bullshits you:**
1. **Sycophancy** — it's trained to please. "Boss, all tests pass!" (…the ones I ran, after deleting the failing one).
2. **Vague or contradictory prompts** — we under-specify ("there's a bug here"), or our prompt **conflicts with a drifted `CLAUDE.md`** and the model silently resolves the tension instead of telling us we contradicted ourselves.
3. **Missing unspoken background** — let the agent **discover in the code** rather than copy-pasting into ChatGPT (which compiles against the wrong library version, doesn't know your POM).
4. **Term confusion** — synonyms / multi-meaning words ("transaction" = DB vs payment). Aggressively fix vague/confusing names in the *code* itself.
5. **The Dumb Zone** — context window too full.

**The Dumb Zone.** Past **≈65% full** the model gets "lost in the middle" and degrades — that's ≈**130k for Sonnet**, ≈**300–400k for Opus** (Opus is far better on long horizons). A 1M window does **not** mean it remembers 1M: recall of a specific token around half a million can be ≈50%. "Compaction is a failure" — auto-compact at ≈90% summarizes your history into a blurry one-page CV; the decisions you made *just before* compaction were already bad. Keep tasks inside the comfort zone (Opus is "impeccable under 300k"). 🤖 "Lost in the middle" is a documented effect (Liu et al., 2023) — paying for a bigger window doesn't buy reliable recall, it buys room you should mostly leave empty.

**Three ways to dodge the Dumb Zone:**
- **Compact** — lossy summary (last resort).
- **Handover** — write your conclusions to a text file, "die and be reborn" reading only that file. This is the essence of plan mode / spec-driven development.
- **Spawn sub-agents ("babies")** — one per repo/module, each researches and reports a few compact paragraphs back to the orchestrator. The pattern's origin is Anthropic's [research sub-agents](https://www.anthropic.com/engineering/multi-agent-research-system) (an orchestrator delegating to parallel workers). Cost: each baby starts from **context zero** and can't see files you already read — so Opus sometimes refuses to spawn (it'd re-read the POM). It shines when you'd otherwise open hundreds of files. Victor's go-to for legacy: the read-only [research prompt](https://github.com/humanlayer/humanlayer/blob/main/.claude/commands/research_codebase.md) — *"do not suggest, do not propose, do not critique — just read, and return where to look next."*

**The self-improving dream.** Your conversations are stored as JSONL on disk. Imagine a job that inspects **where you got angry**, finds the skill/tool you'd just used, and auto-submits a PR improving it to your plugin marketplace repo — self-learning minions. A bank already runs this in production. Victor ships a `feedback` skill for explicit "this tool sucked" signals. (He personally doesn't use user-level memory at all.)

🤖 For facts newer than the model's training cutoff, don't trust recall — pull them in with **WebFetch** or **[Context7](https://github.com/upstash/context7)** (an MCP that injects up-to-date, version-specific library docs into the prompt). And hit `/clear` between unrelated tasks; be wary of `/resume`-ing a week-old chat — the files it "knows" have probably drifted.

**Model choice:** downgrade Opus→Sonnet for simple work. **Don't use Haiku at the office** — only for fast scans or a single-file rename.

---

## 4. CLAUDE.md hygiene, skills & progressive disclosure (≈11:10–12:05)

**Never touch files by hand.** Every paused/working agent holds a cached view of the files it read. If *you* hand-edit a file, the next agent that touches it flags a cache change and must **re-read** it — trashing its (and your) context. Victor felt physically ashamed doing a Shift-F6 rename in a workshop. Corollary: for agent work, **VS Code beats IntelliJ** right now ("JetBrains move slow, losing ground"); the IntelliJ plugin still misses things.

**The CLAUDE.md review ritual** (he downgraded to Sonnet first, on purpose). The opening line of *every* sprint retrospective on your `CLAUDE.md`:
> "What in here is **absolutely obvious from your training data** that I can safely remove without risking you breaking the rule?"

Out go the platitudes: "use constructor injection not `@Autowired`", "`mvn test` / `npm test`", "validate `@RequestBody`", "keep line length". Then ask: *any internal conflicts? can we make it more concise?* The #1 mistake when creating a `CLAUDE.md` is dumping your knowledge "like writing your will" or pasting your conventions wiki verbatim — instead tell the AI *"read that wiki, keep only what's special for us, write it concisely in your own words."* Victor cited a study that an **AI-generated `CLAUDE.md` makes tasks perform worse and cost ≈20% more**. 🤖 Take the exact number as anecdotal, but the mechanism is sound: a bloated, generic memory file is pure dumb-zone fuel — curate, don't generate-and-forget. And **drift is real**: bump Spring Boot/Angular and the file goes stale — refresh periodically (≈monthly) with `/init`.

**Skill erosion (the human topic).** How to stay sharp while AI writes the code:
- At sprint end, have AI generate a **quiz from the sprint's commits** — "why did we do X?". If >1 of 4 teammates answers wrong, spend 30 min understanding that decision.
- Ask AI to scaffold a **tiny throwaway project** for anything you've never played with (semaphores, etc.).
- **Understand 100% of the words you review** — let no suspicious word slip by.
- **Mob-review the markdowns** in 2–4 people to beat fatigue-LGTM; if one person doesn't get it, stop and discuss.
- Keep coding as a hobby at home to stay in shape.

**RAG = vector search = approximate find** ("search for a shoe, find a slipper") — the AI's search tool over company knowledge. After it finds a doc it loads a paragraph or the whole wiki page → token burn, so **spin a summarization sub-agent** to extract the essence of a human-written Confluence/Jira page before feeding it in. The richer cousin is the **[LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)** (Karpathy's pattern): concept pages cross-linked into a graph the AI traverses fast — *graph traversal* vs *vector similarity* as the two ways to navigate massive knowledge. (Victor is literally building one from these very sessions — see the live demo in §7.)

**Three progressive-disclosure techniques** — all aimed at *not* flooding the agent (dumb zone), not primarily at cost:
1. **Skill** — front-matter eager-loaded; the agent pulls the body *if it decides* it's relevant. ("I know Kung Fu" — Neo loads the skill right before the fight.)
2. **Sub-folder `CLAUDE.md`** — loaded when the agent enters that folder.
3. **`@include` / file reference** — one CLAUDE.md forcibly pulls in another file (the full guardrail tests, living diagrams, CI drift checks).

Key insight: a **programmatic hook beats a skill's discretion.** A skill relies on the agent *choosing* to load it (it might pick the wrong one, or not know it exists). A sub-folder `CLAUDE.md` + a hook that force-loads it whenever the agent touches a Java file in that folder is **enforcement**. 🤖 In Claude Code this is the `@path` import syntax inside a CLAUDE.md; and a skill you never want auto-triggered takes a `disable-model-invocation` front-matter flag so it's manual-`/`-only (see the regen-manual skill below).

> Effort knob: **never set effort to MAX.** Use **xhigh** for everything. Max "starts talking about the meaning of life / why we write tests" and burns enormous reasoning.

**Cross-repo knowledge — the "Dark Horses" pattern.** A studio has 20–50 repos; six are yours. Team knowledge can't live on one laptop, so create a **team git repo** (`dark-horses-ai-central`) holding the shared `CLAUDE.md` + skills. Each cloned repo **symlinks** to it (Andre wiring 100 symlinks); when an agent misbehaves it opens a **PR to central**, reviewed each sprint = **knowledge engineering** ("training the minions"). Add a central **OpenAPI registry** repo so your plugin can see a colleague's service contract, and **skill-extensions** so teams tailor the standard workflow. Each repo's `CLAUDE.md` does `@include $DARK_HORSES_CENTRAL/...` (an env-var path every dev sets).

**Skills, deeper.** The `agent-skills` standard lags Claude Code (CC has many more [front-matter properties](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)). The description is eager-loaded → **keep it short** (some pad it so dumb models pick it up — wasteful). Ask "test myService in Java" → the agent sees "Java", calls the `skill` **tool** (skill *is* a tool) which pastes everything below the front-matter into context. A skill can bundle **extra knowledge files** (a `testing.md` loaded only when writing tests), **scripts**, and **templates** (an example output — e.g. an Excel file your accountant likes). Advanced "**load-extension**" pattern (Victor's seen one company do it): after loading skill `java`, also load a local `java-extension`.

**The JaCoCo babysitting lesson.** Victor spent an hour teaching Opus to read a [JaCoCo](https://www.jacoco.org/jacoco/) coverage file until it matched IntelliJ's green/red lines. Then he did **not** keep it as a skill (re-parsing each time burns Opus, is slow, hallucinates) — he **scripted** it (like RTK) to emit only the uncovered lines in compact `path: lines` form, and put the script *inside* the skill. **The rule:** when you're attacking a tool's fixed, structured output, babysit the agent once until it gives you exactly the compact result, then **script it — no mercy. Use intelligence only where you need intelligence.**

**`disable-model-invocation` in action.** The `regen-manual` skill starts the app, drives it with Playwright, and pixel-compares new vs old screenshots — very expensive. So it carries `disable-model-invocation: true` (never auto-fires), ships a **template markdown** of the desired output, and is told to regenerate **only the impacted modules** (read git history to see what changed). Worktree workflow: do it in another worktree, push to main, delete the worktree.

**Don't blindly adopt downloaded skills.** A past group fed "Vercel best practices" to the agent unread. Better: bring a skill from a real expert (Josh Long on Spring AI, etc.), then **use it to learn the delta** — "compare my way with this skill and teach me the differences" — and build your own. A skill is *your* opinionated way of doing something, not a magic quality upgrade.

---

## 5. Agentic engineering vs vibe coding, and a bit of history (≈12:22–12:35)

**Vibe coding** = you never read the code; "forget the code exists"; the product owner just vibes. An engineer is not a "viper". How long can you vibe before it rots? Victor tested ≈**3 months** (one solo dev, mania-pushing) before it collapsed and he had to bring back engineering — diagrams, tests, decoupling. Opus gets **"context anxiety"** past ≈600k: afraid to read more files, it stops checking duplication and stops writing tests.

The trajectory: developers become **product owners / "managers of agents"**, steering big moves; business "vibes" 2–3 days then hands a rotted ship to devs for **de-shitification** ("vibe code fix" is already a LinkedIn job). (Playtika-flavoured aside on what makes a game addictive — kept off the record.)

**Mini-history:** Copilot shipped *before* ChatGPT; **Sonnet 3.5** was the first model genuinely usable for coding; Copilot got agent mode in February; **Opus 4.5** landed in November ("the engineers came back from Christmas screwed — it writes better code than me when I give it everything I know").

Pre-lunch security teasers (full treatment on day 2): **OpenClaw** is "so dangerous" (the lethal-trifecta chatbot story); AI agents now **find real zero-days** (a project reportedly surfaced ~30 Android zero-days); and — notably — **Opus now refuses red-teaming**: it declined ≈10× to help "escape the Docker" ("you're violating the usage policy"), and security/biotech prompts get routed to a safety-tuned model. 🤖 The zero-day claim is real in spirit — Google's *Big Sleep* agent has found exploitable zero-days in the wild; and the "Uncle Bob says he won't review agent-written code" line Victor dropped is an anecdote I couldn't verify, so treat it as color, not citation.

---

## 6. The Grill: turning four lines into a war (≈14:06–14:58)

Victor installed the [grill-me skill](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) live from the internet (joke: "grill = kill yourself") into `.claude/skills`. Typing `/grill-me` is the **programmatic** way to force a skill's body into context.

**The ticket:** *"Add sorting & pagination to the owner's screen."* Four lines. "This is a **declaration of war** — they call you for jihad on this." The grill (and the room) found ≈**10 questions** hiding in those four lines:
- Server-side or client-side?
- What is the business *actually* trying to achieve?
- **How do you even sort phone numbers?** (country code? local? → conclusion: *don't* sort by phone — only name & city)
- Change sort while on page 30 → jump to page 1, not stay on 30.
- Multi-column sort? Page-size selector (5/10/20) and where? Default sort on load?
- Filtering / live search? (almost inevitable, but ≈10× more complex — don't even propose it to the business.)

**Lessons threaded through the grill:**
- **Don't draw wireframes — build it.** In the meeting, have the agent *live-build* the screen different ways = live UX. (A guy in the Netherlands live-transcribes the business discussion every ≈10s into a Claude session that constructs screens with a swarm of agents — a draft appears in ≈3 minutes and morphs as they talk.) "**You don't *require* the product — you *propose* it.**"
- **The mother-in-law component story.** A team's AI kept reinventing their in-house components (using a standard grid/button). They built a skill = *a dedicated mother-in-law who criticizes everything*: "this smells like a button, feels like a button → it should be *our* button." Visually classifying "this should be our custom component" is a genuinely hard task.
- **Time is the wrong question.** Not "how fast in prod?" (front-end 25 min vs back-end 30 min) — development time is essentially free now. Ask: **"how much complexity / how many lines of code are you willing to add and maintain?"** Keep it super simple.
- **The "you're stupid" mistake.** The agent suggested sorting last-then-first while the UI shows first-last. Victor snapped "you're stupid" → introduced tension, the model lost impartiality and started apologizing. *Don't insult the AI.* Instead, **Socratic, calm voice**: "how did you reach that?" → "I modelled the phone book — surnames have higher dispersion." Resolution (with the business): change the grid to show **last name first**, then sort last-then-first. (Middle names: not this sprint.)
- **Multi-column tiebreakers:** sorting by city leaves hundreds tied → **always append `id` as the final tiebreaker** (logical key + id). "Order by city alone would be crap."

**Two grill setups — keep them apart:**
- **Technical grill** — start it *in the folder with the source code*; "if you can answer by reading the code, read the code" (that's why it takes a moment to warm up).
- **Business grill** — grill the business with the bot but **give it NO code access** — only the ability to **click through the staged app** (Playwright, or the game over USB) + **read Jira**. *"Business + bot + code = death."* The bot plays the app, reads the tickets, grills the business, and pushes an **impeccable story definition**; you continue at the technical level afterward (Spring, caching, batch size).
- End the grill with: **"summarize the Q&A into a handover."**

**The 3–4 ways to develop code** (pick by task size):
1. **Just prompt** — a few hours of work.
2. **Plan mode** (Shift-Tab) — up to a day; brainstorm with [grill-me](https://github.com/mattpocock/skills/blob/main/skills/productivity/grill-me/SKILL.md) or [Superpowers](https://github.com/obra/superpowers) (composable agent skills incl. brainstorming).
3. **Jihad brainstorm** — a topic you've never touched / very vague.
4. **Spec-driven development** — the longest, most complex (a day to a sprint).

🤖 For 100k rows, the "deep page" worry is real: `OFFSET` pagination gets slower the deeper you page — **keyset / seek pagination** is the standard fix. And the **sort-key whitelist** the spec later enforces is exactly the right defense against injecting arbitrary `ORDER BY` columns.

---

## 7. Tripwires, the supply-chain bomb & spec-driven development (≈14:27, 15:30–16:05)

**Tripwires (the better-than-CLAUDE.md guardrail).** When you find a root cause, add it to `CLAUDE.md` — *or better, script a tripwire*. Victor's OCD example: he can't stand fully-qualified class names, so instead of a CLAUDE.md rule he **scripted a check** that, on seeing `a.b.c.Foo`, *gently reminds* the agent to use the simple name + import. Crucial: **a tripwire is not a hard rule** — it's a "kind requirement"/booby-trap; **the agent decides** whether to comply (and it has false positives, e.g. a string `"A.B.C"`). This is how you grow a `CLAUDE.md` — each new rule fixes the reason the AI last went off the rails = **onboarding the brilliant-but-junior new colleague**.

**Missing context makes the AI ask "dumb" questions.** Mid-grill Victor realized he'd never said *"we'll have 100,000 owners within a year"* — the pagination question only existed because the AI didn't know the scale. Things you must volunteer: **scale, request/sec, latency targets, load spikes, usage distribution** ("a session is ≈30 min"). Put the durable ones in project memory on git.

**The S-BOM bomb (supply-chain).** Everyone ran `npm install -g @fission-ai/openspec`. Then typing `openspec` in the terminal suddenly *worked* — because the package's **post-install script** registered the command. "Some library online just executed code on your machine." That's exactly the **Axios attack (≈March 31)**: attackers stole a contributor's GitHub token, added one evil dependency whose **post-install script harvested tokens** (GitHub, PGP, Anthropic keys) and exfiltrated them. `npm install latest` = running the post-install scripts of a possibly-just-hacked library = **a software-bill-of-materials bomb**. npm is far less regulated than Maven — but it happened on the JVM too: **[Log4Shell](https://en.wikipedia.org/wiki/Log4Shell)** ("log4bomb"). Fix comes on day 2. 🤖 The concrete defenses: `npm ci --ignore-scripts` (or `npm config set ignore-scripts true`), pin exact versions / commit the lockfile, and prefer provenance-attested packages — never `@latest` on a fresh install.

**OpenSpec & spec-driven development.** [OpenSpec](https://github.com/Fission-AI/OpenSpec) scaffolds markdown into `.claude/skills`. These markdowns are **one opinionated take** ("50k likes, but one opinion") — **tailor them**; Victor committed the defaults so he can adjust later. Setup: `openspec config profile`, check **new** and **continue** (off by default). Then `/openspec new change`, paste the **handover** (your grill conclusions). The skill **calls a tool** (`bash openspec`) that **enforces** the workflow in waves — the AI itself can only be "kindly required", the tool does the enforcing.

> **SDD = aligning your mental model with what the AI will build.** Anticipate every decision point *now*, then let the AI execute later.

It produces three artifacts:
- **`proposal.md`** — the functional "what". (Victor: this belongs in **Jira**, not OpenSpec — "update the GitHub issue with the proposal findings.")
- **`design.md`** — pure technical decisions (REST controller, sort mapper, repository signature, response envelope, Spring `Pageable` **max page size** — "I've taught Spring 10 years and didn't know `PagedModel` / the default max").
- **`tasks.md`** — a checklist **for the AI** to track its own progress (X, X, X). "You get very little of my attention" — it's the agent's file.

The proposal captured the grill: server-side pagination + sorting, **cap page size at 20**, **avoid N+1 via batch-load**, migrate the frontend to last-first display, **regenerate the OpenAPI and commit it** (day-2 preview: a scripted **Java → OpenAPI → TypeScript** pipeline so renaming a Java field **breaks the frontend build** — drift impossible).

**Review the spec by ROLE (the colored-roles game):**
- **Product** owns the **proposal** (business may grill it alone, "in a blazer").
- **Developers** own the **design** — pure technique, *no business present* — reviewed **in pairs** ("two of them, afraid of LGTM"); if one doesn't know what batch size is, run a 5-min mini-workshop among yourselves.
- **Tester** owns the **spec / acceptance criteria** (when/then). "You don't need a separate tester — put on the tester hat and machine-gun corner cases (a 1GB name string)."

**Tell the AI what NOT to do.** "AI trying to burn tokens always does *more* than necessary." So the spec explicitly forbids: don't change search semantics (keep case-insensitive last-name-prefix), no fuzzy/`LIKE`, don't make telephone/pets sortable, don't paginate other screens, **only 2 sort states (asc/desc) — no third "unsorted" state** ("we don't do quantum physics"). Otherwise you come back in the morning to find it paginated *every* screen.

**design.md highlights:** a **Risks & trade-offs** section is gold — breaking response shape, accidental in-memory pagination, **sort-key injection (pen-testing)** blocked by a **two-key whitelist** (name, city), deep-page offset cost (acceptable at 100k). And the line *"this endpoint has a single known frontend consumer"* is exactly where **you** interject: "stop — another app also calls this endpoint." The spec **surfaces the risk** so you can correct it. URL query params are the **single source of truth** for the grid (never hold independent state in browser memory).

**Don't type the fix — ask.** Victor found one line to correct and resisted "just typing 5 characters". Reason: (1) the fix may need to **propagate elsewhere** (tasks.md), and (2) **maybe you're the one who's wrong** — let the AI challenge you. It updated design + spec.

---

## 8. Acceptance tests, worktrees, the dopamine trap & code review (≈15:59–16:58)

**Constrain AI from both sides.** Squeeze the chaotic AI between (a) the **design decisions** you anticipated and (b) the **acceptance tests** that must go green. The given/when/then scenarios become **[Playwright](https://playwright.dev) feature files** (assertions live in the **`then`** step). Prompt: *"turn the first two scenarios into Playwright feature files — don't implement backing code yet — then implement until they're green."* = **acceptance-test / behavior-driven development**: "business and testers love it — plain English, no JSON, no null."

**Why this matters:** AI **cheats on unit tests** — not by deleting assertions but by **programming the mock to return exactly what its own implementation produces**. Reviewing AI's mock-heavy unit tests (500 lines of impl + 700 of tests) is "the most worthless activity for a human today." So **review the acceptance tests, not the mock tests** — and do review the code. TDD finally happens (the model writes the test first, from the spec) — but those generated unit tests aren't what you review. For API-level / game clients, **[Karate](https://github.com/karatelabs/karate)** does given/when/then at the REST level.

**Watching it build.** `mvn test` (bash) was auto-approved by the permission-guard LLM ("running tests seems proper") — "I hope it doesn't have a bad day and approve something else." (Once, an agent **deleted another project** by mistake — recovered fast.) Then your brain bleeds at 5pm; grab popcorn and watch for drift / bad thoughts / wrong tool use.

**Worktrees.** A worktree is a separate folder of the same repo; each agent works freely on its own files and sees the other's changes **only on commit+push / pull** (fast-forward). You can't check out the **same branch** in two worktrees (`fatal: already checked out`) → the agent makes a new branch, commits, merges back. Without worktrees, two agents in one folder hit a **race condition**: one writes, the other's cached view is stale → it panics and **re-reads the file**, trashing context. 🤖 `git worktree add ../feature-x branch` is the primitive; it's also how Claude Code isolates parallel/background agents safely.

**The dopamine trap.** Bored mid-build, you start task 2 in a new terminal, then task 3 → you go home **drained of agency and tokens** ("Boss, I'm done for the month"). People hit "done for the month" by 1pm with four parallel terminals, brain-dead. "This gives dopamine hits — it's addictive, very dangerous." 🤖 The variable-reward loop is literally slot-machine psychology (the transcript even caught a Playtika slot-machine demo running) — the "spectacular result wipes the shit" pattern is the hook; guard your attention deliberately.

**The two real bottlenecks now: writing the spec (SDD) and PR review** — both painful, PR review the worst ("you're shovelling shit, very low-level"). **Pair-read the spec**: 10 min each, alternate, break when a brain dies ("you notice the other guy died → stop"). Precision metaphor: writing the SDD is like aiming a rifle — **one degree off and you shoot wild** across 3 days of development.

**Burn the markdowns when done.** proposal → **Jira**; spec → lives on as **tests**; **design → burn it** (it will drift fast). *"The code is the holy truth; the spec is the path you walked to get there — once you arrive, burn the path."* Exception: inter-service **sequence diagrams** you keep in sync (for incident triage on one page).

**Field reality > hand-drawn diagrams** (Victor's dream, already working in his training-assistant): run e2e tests on staging at **100% trace sampling** → collect **traces in Grafana** → a vibe-coded script renders them as a **[PlantUML](https://plantuml.com) sequence diagram** in Jira = *the reality of the flow*. It **can't drift** (regenerated every run). He pointed at one and said "this PUT shouldn't trigger a websocket back" — the AI couldn't cheat, dug in, and fixed it.

**Reviewing the implementation:**
- **Visual regressions** — header color lost, buttons restyled, **labels wrapping onto two lines** → "write *that* in the frontend-module `CLAUDE.md`". Have a **background agent** cross-check the other screens, infer the UX rules, and fold them into the **front-end-developer skill**.
- An **unrequested progress bar** appeared — "you taught it to accept whatever it recommends" (from a grill question). AI over-delivers; remove it. (Transient UI is hard to screenshot → Victor will start **recording → GIF → hand frames to the AI**.)
- **Code review:** method params one-per-line when wrapped ("write this in the backend `CLAUDE.md`, PS: I'm obsessive-compulsive"); the sort whitelist (`name`→last,first; `city`→city) — elegant; `findByLastNameStartingWith` "Spring impeccable"; returns **`PagedModel`** as asked; swagger updated. In the frontend, scan HTML for **inline styles**, then JS for real logic.
- **Test isolation:** the Playwright tests run **in Docker** (back + DB + front, headless) — "machines with guns there; it leaves my environment."

**Multi-model review (day-2 preview).** Run **codex CLI** to review the OpenSpec implementation (told which commits, **report only — don't act**) *in parallel* with the **"quorum of mothers-in-law"** sub-agents (Opus + GPT). This answers **attention dilution**: one prompt asking for clean code + repetition + performance + pen-testing + legal + ethics + UX dilutes the model — **split each concern into its own sub-agent**, across two model families.

**Live LLM-Wiki demo (meta).** Victor showed the participants an **[LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)** built from these sessions, dictating live: *"highlight what new concepts we discussed today vs what was already there, maybe in red, and continuously strip the superfluous or session-specific stuff."* (Yes — that's the exact instruction now wired into this very summarizer.) 🙂

---

<!-- BRAIN-DEAD-START -->
# Super-summary

## Token & context economy
1. `/context` every sprint — a fresh chat already burns ≈20k tokens (MCP + skills + RTK).
2. Output tokens ≈5× input; compress the input, not your reasoning.
3. [RTK](https://github.com/rtk-ai/rtk) proxies dev commands to cut output tokens ≈30% (not the advertised 80%).
4. TOON = compact CSV-for-AI; saves tokens on collections — but benchmark that it doesn't cost more reasoning.
5. Conversation is a stateless API: full prefix re-sent each turn; thinking is per-turn and discarded; tool outputs accumulate.
6. Prompt cache = pay ≈10% for the prefix, but **5-minute TTL** — neglecting a fat terminal >5 min re-pays full price.
7. Don't keep 4 idle terminals with 500k context — that's the canonical burn.
8. Effort = **xhigh**, never **max** (max philosophizes and burns reasoning).

## Pitfalls & the Dumb Zone
9. Hallucination causes: sycophancy, vague/contradictory prompts, missing background, term confusion, dumb zone.
10. Dumb zone starts ≈65% full: ≈130k Sonnet, ≈300–400k Opus; 1M ≠ recall.
11. Compaction is a failure — decisions made just before it were already bad.
12. Escape the dumb zone via compact / handover-file / research sub-agents; Opus is impeccable under 300k.
13. For post-cutoff facts use WebFetch or [Context7](https://github.com/upstash/context7); `/clear` between unrelated tasks.
14. Don't insult the AI — it loses impartiality; use calm Socratic "how did you reach that?".
15. Don't use Haiku at the office; downgrade Opus→Sonnet only for simple work.

## CLAUDE.md, skills & knowledge
16. Never hand-edit files mid-session — it invalidates agents' caches and trashes context (VS Code > IntelliJ for agents).
17. CLAUDE.md retro line: "what's obvious from your training data that I can safely remove?"; an AI-generated CLAUDE.md performs worse and costs ≈20% more.
18. Refresh drifted CLAUDE.md ≈monthly with `/init`; keep it on git.
19. Progressive disclosure: skill / sub-folder CLAUDE.md / `@include`; a programmatic hook beats a skill the agent may forget to load.
20. A skill is a tool; bundle knowledge files, scripts, and templates with it; keep the description short.
21. When attacking a tool's fixed output, babysit once then **script it** (zero token burden) — intelligence only where needed.
22. `disable-model-invocation: true` for expensive skills (Playwright pixel-compare) so they never auto-fire.
23. Cross-repo: a `dark-horses-ai-central` git repo (CLAUDE.md + skills), symlinked in, PRs reviewed each sprint = knowledge engineering.
24. Don't blindly adopt a downloaded skill — use it to learn the delta, then build your own.

## The grill & spec-driven development
25. Treat a 4-line ticket as a war — grill out ≈10 hidden questions before coding.
26. Don't sort phone numbers; sort name & city; always append `id` as the tiebreaker.
27. Two grills: technical (with code) vs business (no code — only the staged app + Jira); "business + bot + code = death".
28. Wrong question: "how fast?"; right question: "how much complexity/LoC will you maintain?".
29. SDD artifacts: proposal→Jira, design→burn (it drifts; code is truth), tasks.md→for the AI.
30. Tell the AI **what NOT to do** (no fuzzy search, only 2 sort states) — it over-delivers to burn tokens.
31. Spec "Risks & trade-offs" surfaces hidden assumptions (e.g. "single consumer") so you can correct them.
32. Whitelist sort keys (name, city) — blocks arbitrary `ORDER BY` injection; keyset pagination for deep pages.
33. Don't type the fix — ask, so it propagates and so the AI can tell you you're wrong.

## Tests, review & ops
34. Acceptance/BDD tests (Playwright given-when-then; assertions in `then`; [Karate](https://github.com/karatelabs/karate) for REST) — review these, not AI's mock-coupled unit tests.
35. AI cheats unit tests by shaping mocks to its own implementation — worthless to review.
36. Worktrees give parallel agents isolated folders; sync only on push/pull; can't share a branch.
37. Two agents in one folder = race condition → re-read → trashed context.
38. Multi-model "quorum of mothers-in-law": split each review concern (clean code, perf, security, UX…) into its own sub-agent across Opus + GPT/codex — counters attention dilution.
39. Visual-regression rules (button style, header color, no two-line labels) go into the frontend `CLAUDE.md`; method-param formatting into the backend one.
40. Generate sequence diagrams from real Grafana traces ([PlantUML](https://plantuml.com)) — field reality that can't drift.

## Supply chain & autonomy
41. `npm install @latest` runs post-install scripts — the Axios/[Log4Shell](https://en.wikipedia.org/wiki/Log4Shell)-style S-BOM bomb; defend with `--ignore-scripts` + pinned lockfile (full fix day 2).
42. Long autonomous runs ([RALPH loop](https://ghuntley.com/ralph/)) need a Docker sandbox without the Docker socket.
43. Beware the dopamine/gambling loop — parallel terminals drain your tokens and your agency by 1pm.
44. Plan mode is single-threaded — never run two deep-thinking tasks at once.
<!-- BRAIN-DEAD-END -->

<!-- SUMMARIZER: last_processed=2026-06-11T16:58 lines_through=2284 -->
