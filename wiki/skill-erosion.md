---
title: Skill Erosion
category: concept
tags: [human-factors, learning, review, team, quiz, mob-programming]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-05-25-ai-government]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-06-11
updated: 2026-10-13
---

Skill erosion is the gradual atrophy of an engineer's own competence when AI writes all the code — the human risk that mirrors the machine risk of [[drift]].

## The evidence

- <span style="color:red">An Anthropic randomized trial (Jan 2026): the AI-assisted group scored **−17%** on a comprehension quiz, with the biggest gap in **debugging** ([Anthropic research](https://www.anthropic.com/research/AI-assistance-coding-skills)).</span>
- <span style="color:red">[METR (Jul 2025)](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/): experienced open-source developers were **19% slower** with AI while believing they were 20% faster ([[2026-09-02-ai-insurer]]).</span>

## Why it happens

- <span style="color:red">**The boring reps disappear.** Constant System-2 work (races, consistency, caching) removes the System-1 repetitions that consolidate fundamentals — the loss isn't syntax, it's the meditative reps that bake in concurrency intuition ([[system-2-fatigue]], Steve Yegge's [The AI Vampire](https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163); [[2026-05-27-ai-open-workshop]]). Counterpoint heard in the room: *"Java is the new assembly — you can't write x86 and you're still an engineer."*</span>
- <span style="color:red">**Skill loss happens to people who stop *reviewing carefully*, not to people who stop typing.** The real risk isn't AI quality but **fatigue-driven rubber-stamping** — the moment you stop owning your own design ([[2026-05-27-ai-open-workshop]], [[2026-07-23-ai-software-product]], [[approval-fatigue]]).</span>
- <span style="color:red">**"Epistemic dopamine":** the feeling of having learned from AI is a drug, not learning; the only test is explaining it to someone else ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">**The unknown grows faster than the known:** the more you know, the contact surface with the unknown grows (quadratically, in one trainer's phrasing) — juniors don't know what they don't know, seniors feel like impostors. **Bus factor 1 is a business liability, and AI hides it** ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">**Decided minds can't learn:** go to Opus with **questions, not answers** — someone who already decided can't accept the better option. "Markdown fatigue" — specs that feel heavy — often means you don't know the engineering they name ([[2026-09-04-ai-software-company]], [[cognitive-debt]]).</span>
- <span style="color:red">Delegated workflows teach nothing by default: a developer who let an auto-activating TDD workflow ([[superpowers]]) run end-to-end produced good code and "learned nothing" ([[2026-07-01-ai-java-community]], [[2026-07-03-ai-bank]]).</span>

## Never approve what you don't understand

<span style="color:red">The core rule, restated by nearly every session: **if a word or concept is unclear, stop.** "If a single word is not clear for the junior in the team, you unleash jihad." "Do not assume you know this shit. You don't." ([[2026-07-22-ai-agentic-how]]). The biggest mistake of the year is "looks good to me" without understanding every comma ([[2026-09-14-ai-industrial]]); "yes-yes-yes to Opus without challenge leaves you jobless" ([[2026-10-01-ai-bank]]).</span>

- <span style="color:red">**Ask for a lab, not an explanation.** When the AI proposes a concept nobody understands (one team hit *collation* in a sort spec), ask for a **minimal scaffold — ≈100–300 lines — with unit tests you can run**, and freeze the design until the team gets it. Do it in a **separate conversation** so you don't burn or derail the working context ([[2026-07-22-ai-agentic-how]], [[2026-09-07-ai-bank]], [[2026-10-01-ai-bank]]). Spend half a day if needed, and **say so publicly** — at stand-up, *"I'm at question 7 of 20"*, not "looks good to me" ([[2026-07-23-ai-software-product]]).</span>
- <span style="color:red">**Escalate when the solution exceeds you:** have a second agent explain it, then go to the elder, until someone can — "phone a friend" ([[2026-09-16-ai-safety-critical]], [[codeowners-elders]]).</span>
- <span style="color:red">**Understanding is risk-proportional:** an internal tool — passable; ambulance or safety-critical software — you understand and approve every decision ([[2026-09-16-ai-safety-critical]]).</span>
- <span style="color:red">**Never leave unsupervised, however much you vibe:** contracts with the outside (Swagger / AsyncAPI / Avro), **what you persist in the DB** (garbage there stays), and critical and complex flows ([[2026-08-13-ai-insurer]]). You may stop reading the code someday, but **the design decisions from the interview must be understood 100%**; learn to detect your own "drool mode" — LGTM without reading ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Arrive with a hypothesis:** think 5–10 minutes alone (on paper) before prompting, or warm up by guessing what Opus will ask you ([[2026-08-27-ai-it-services]], [[2026-09-14-ai-industrial]]).</span>

## Staying sharp

Concrete countermeasures for keeping a team's skills from rotting:

- **Sprint quiz from commits.** At sprint end, have the AI generate a quiz *from the sprint's commits* — "why did we do X?". If more than one of four teammates answers wrong, spend 30 minutes understanding that decision. <span style="color:red">Self-test: would *you* pass it on the decisions you approved? ([[sprint-design-quiz]], [[2026-10-07-agentic-reconversion-devoxx]])</span>
- **Throwaway scaffolds.** Ask the AI to scaffold a tiny throwaway project for anything you've never played with (semaphores, etc.) to actually learn it. <span style="color:red">Keep them: a personal repo of ≤200-line prototypes of everything you finally understood (outbox + Kafka, REST idempotency, Hibernate 2nd-level cache), like [Simon Willison's TILs](https://til.simonwillison.net/) ([[2026-07-22-ai-agentic-how]], [[2026-08-31-ai-software-vendor]], [[2026-09-18-ai-agentic-how]]).</span>
- **Understand 100% of the words you review** — let no suspicious term slip by. Aggressively rename vague code; term confusion is also a [[hallucination]] cause.
- **Mob-review the markdowns** in 2–4 people to beat fatigue-LGTM; if one person doesn't get it, stop and discuss. <span style="color:red">It works through the [Hawthorne effect](https://en.wikipedia.org/wiki/Hawthorne_effect) — you pay attention when watched — so **don't be alone in front of Opus**, and never grill alone on a 2+ day task. One company with four people on one markdown calls it "our learning time"; another reads the markdown aloud, taking turns, stopping at any word not understood ([[2026-06-05-ai-consultancy]], [[2026-09-02-ai-insurer]], [[2026-09-14-ai-industrial]], [[mob-spec-review]]).</span>
- Keep coding as a hobby to stay in shape.
- **One quarter-sprint hands-only** — accept shipping 5–10% of usual velocity; the skill return is worth it. <span style="color:red">The team-wide variant is a **redemption sprint**: one sprint per quarter where everyone hand-writes everything and ships ≈20% less, telling the business why ([[2026-05-27-ai-open-workshop]]). Lighter variants: **one day a month without AI**, applying Ask-mode edits by hand ("run the code through your fingers") ([[2026-10-01-ai-bank]]).</span>
- **Pair/mob programming** — only 2+ developers working together can match an Opus-level agent on the same task. It is the gold standard for maintaining craft. Reading code at scale is also an emerging bottleneck: the more agents write, the faster review skills must grow.
- **[[grilling-in-reverse]]** — have the AI Q&A you on what existing code does (one question at a time), to verify you actually understand what you approved. The "brilliant!" technique: the same conversational format as [[grill-me]] but reversed — AI reads the code, you answer. Running it before signing off a PR surfaces exactly the decisions you rubber-stamped.
- <span style="color:red">**Make the agent a tutor.** After a bug fix, continue *"explain how you found it"*; or create a `/teach-me` command — *"turn into my personal tutor; no more than two sentences at a time"*. *"I don't want to remain stupid — I grow next to you."* ([[2026-04-07-ai-product-team]]) Claude Code's [output styles](https://code.claude.com/docs/en/output-styles) build this in: **Explanatory** makes it a teacher; **Learning** implements ≈half to 90% and leaves you the core piece ("Victor, your turn"), then checks it — use it at least once a week ([[2026-09-02-ai-insurer]], [[2026-09-18-ai-agentic-how]], [[2026-09-28-ai-gaming-tech]]). Have it **explain every word** you don't understand, **argue its decisions** with pros/cons, and keep a `docs/` folder of what/why/decisions with diagrams ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">**Plant intentional subtle mistakes** in PRs to verify the review is real — a cousin of [[prompt-injection-canary]] ([[2026-08-31-ai-software-vendor]]).</span>
- <span style="color:red">**Slice work small enough to hold in your head** — a 5-hour feature is chaos at the first bug ([[2026-07-09-ai-live-casino]]).</span>
- <span style="color:red">**Feel the difference:** implement the same feature 3× from a clean git — raw vibe → + code review → grilling + superpowers ([[2026-07-01-ai-java-community]]).</span>
- **Vibe-code in an unfamiliar language** (calisthenics) — spend 1–2 months generating code in a language you can't read fluently (e.g. Swift or Python as a Java developer). This forces you to judge *outcomes*, not code aesthetics — the mental shift that underlies agentic engineering. Also: start each day's first task pretending you have no IDE, only the CLI. <span style="color:red">Reconversion takes ≈2–3 months, fastest on a **passion project** on a personal subscription — the unknown language removes the temptation to touch the code; at ≈3 months cracks appear and you bring engineering back ("vibe horizon") ([[2026-06-02-ai-open-workshop]], [[2026-08-13-ai-insurer]], [[2026-10-01-ai-bank]]). Note this is a **training drill, not production practice**: shipping code nobody on the team can read is blind review ([[2026-09-02-ai-insurer]]). The full ladder of such drills — no file edits, problem-not-solution, 4 parallel conversations, zero pasting, a CLI-only day, all prompts by voice — is on [[ai-calisthenics]].</span>

This is the human half of the [[multi-model-review]] discipline (pair-reading the spec) and a direct consequence of [[vibe-coding]] at scale — the more you delegate, the more deliberately you must invest in understanding what was delegated. The human cost compounds in isolation — the "up till 4 a.m. arguing with the agent" loneliness — so the antidote is social (mob/pair) as much as technical; and the durable countermeasure to delegation is learning to *feel* the problem class (a concurrency bug, a deadlock) well enough to judge a fix, the same thesis as the [[feedback-loop]].

## Budget the brain and the time saved

**Front-load the analysis.** The reflex worth fighting is rushing to prompt before you've thought: a spec or plan **agglomerates ≈7 days of decisions into ≈1 hour**, and if you can't *imagine* where the agent will go you lose the thread after decision #4. The rule underneath it all, even when you're alone: **if you don't understand something, stop — never approve what you don't understand** (the core of honest [[code-review]]).

<span style="color:red">**Cognitive burnout is real, not a metaphor.** The 5-day task now takes 2–6 hours, and the hour of thinking on the markdown compresses a week of thinking. Budgets quoted: ≈2–4 hours of System-2 work a day (Yegge); pause or detox after 3–4 hard questions (Victor lasts 7–10 a day on a good day). Schedule design with a full battery — a dead brain says "yes, works" to questions it no longer processes ([[2026-08-13-ai-insurer]], [[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]], [[ai-addiction]]).</span>

<span style="color:red">**Reinvest the saved time in learning.** An 8-hour task becomes 2 hours with the agent + 2 hours of learning ("don't tell the boss", like refactoring); half of the saved time goes to learning (5 days become 1.5, plus a study day). A good employer is one that grants **conscious learning time** during the reinvention. "A day you close the laptop without having learned anything is a lost day" — complacency plus cognitive fatigue are the first to go next year ([[2026-08-27-ai-it-services]], [[2026-08-31-ai-software-vendor]], [[2026-09-02-ai-insurer]], [[2026-09-07-ai-bank]]). **Be a 10× learner first, a 1.3× doer second** — measured gains today are ≈30% ([[2026-07-23-ai-software-product]], [[2026-09-14-ai-industrial]]).</span>

**"Yeah okay Claude" — the famous last words.** The danger is not noticing where your brain died and pushing the approval anyway. Parallel agent terminals create a *second* dumb zone: you cannot think deeply about concurrent resharding in one terminal and hardcore caching in another simultaneously — you die. Walk, sleep, take a break before approving anything in that state. <span style="color:red">When you feel fog or anxiety, take a break ([[2026-09-14-ai-industrial]]).</span> See [[cognitive-debt]] for the collapse arc.

## What knowledge survives

- <span style="color:red">**Three AI-era engineer skills:** imagination (learn from the business what to dream), critical thinking (be the "mother-in-law" of AI output — it needs CS fundamentals to see design trade-offs), and adaptability (unlearn to relearn — it hurts more the more senior you are). Fundamentals (algorithms, DBs, distributed systems, security) gain value; what becomes obsolete is *how you execute*, and junior-sized tasks like a filter screen are disappearing ([[2026-05-25-ai-government]]).</span>
- <span style="color:red">A live triage in one session: "dead" — Clean Code, Design Patterns, Performance Tuning, JPA, Spring how-to ("the agent finds it better than all of us"); "survives" — testing, event-driven design (eventual consistency, idempotency), microservice API design, secure coding against [[prompt-injection]], and strategic DDD (event storming, bounded contexts, ubiquitous language), while tactical DDD no longer matters. The room's counter-argument was accepted on the spot: the "dead" concepts remain necessary **as vocabulary** to evaluate what the agent proposes ([[2026-09-07-ai-bank]]).</span>
- <span style="color:red">**Learn the fundamentals the hard way first — you stay accountable for AI code.** Patterns only emerge after memorising; [Shuhari](https://en.wikipedia.org/wiki/Shuhari) — learn, break, transcend the rules ([[2026-09-24-ai-bank]]).</span>
- <span style="color:red">**Become an expert learner:** active recall, spaced repetition, chunks of four, dual coding ([Make It Stick](https://www.hup.harvard.edu/books/9780674729018)). Grow a **tester mind and a bullshit detector** for what the agent claims, and **speak up on any gut feeling — intuition is where we still beat it**. Beware echo chambers: no shared practices exist yet, and every team's workflow drifts apart ([[2026-10-07-agentic-reconversion-devoxx]], [[agentic-reconversion]]).</span>
- <span style="color:red">Ego check: the AI taught a 16-year Spring trainer about Spring Data's `PagedModel` — "you will never be the smartest person in a room, even if you are alone" ([[2026-07-22-ai-agentic-how]]). And the identity shift: "from code typist to product builder: love the product, not the code" ([[2026-09-14-ai-industrial]]). One seasoned clean-code engineer after months of agentic work: *"The code looks dirty to me — like cutting corn by hand when the combine exists."* ([[2026-05-25-ai-government]])</span>

## Hiring and juniors

- <span style="color:red">**The new interview: explain the solution Opus gave you.** Opus solves the LeetCode problem in front of the candidate; the grade is the *explanation*. A good candidate makes the AI work for their understanding — rebuilds the solution from naive to tuned, asks for examples and a scaffold proving it works, sets a second adversarial agent hunting slop — then closes everything and explains the core idea. A large tech company replaced LeetCode this way ([[2026-09-14-ai-industrial]], [[2026-10-01-ai-bank]]).</span>
- <span style="color:red">**Hire juniors** — it is in seniors' own interest: nothing tempers a senior like a junior asking, and juniors pick up agentic work faster ([[2026-08-27-ai-it-services]]).</span>
- <span style="color:red">Etiquette: [Don't paste the AI](https://dontpastetheai.com/) — whoever asks you something wants *your* answer, not a pasted AI explanation ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">In ≈2 years review happens on GitHub or the phone, not in the IDE — one more reason to train judgment rather than typing ([[2026-10-01-ai-bank]]).</span>

## See also
- [[vibe-coding]]
- [[drift]]
- [[hallucination]]
- [[multi-model-review]]
- [[spec-driven-development]]
- [[code-review]]
- [[feedback-loop]]
- [[grilling-in-reverse]]
- [[cognitive-debt]]
- [[sprint-design-quiz]]
- <span style="color:red">[[ai-calisthenics]]</span>
- <span style="color:red">[[system-2-fatigue]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[mob-spec-review]]</span>
- <span style="color:red">[[agentic-reconversion]]</span>
- <span style="color:red">[[ai-addiction]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
- [[2026-06-26-ai-agentic-how]]
