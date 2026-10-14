---
title: Code Review
category: pattern
tags: [code-review, pull-request, judgement, sorted-review, attention, pairing, stacked-prs, adversarial-review]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-26-ai-agentic-how]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-06-ai-medical-diagnostics]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-13-ai-insurer]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-18-ai-agentic-how]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]"]
created: 2026-06-11
updated: 2026-10-13
---

Code review in the agentic era is the human's last, scarcest gate: once agents write most of the code, reviewing it becomes one of the two real bottlenecks left (the other is writing the spec), so the whole craft is about spending a fading human attention budget where it changes the outcome.

<span style="color:red">By mid-2026 the bottleneck claim was the consensus: writing code stopped being the bottleneck, review became one — it's in most teams' top-3 time sinks. *"You can never run faster than what you can comprehend"* — you stay the bottleneck on purpose ([[2026-08-10-ai-consultancy]], [[2026-06-02-ai-open-workshop]]).</span>

## The human reviews judgement, not mechanics

It is degrading for a human to flag "this method is too long" or "too many parameters" when a machine can detect it. So everything mechanical is pushed onto the [[static-analysis-gauntlet]] (and [[tripwire|tripwires]]) until the code self-repairs and reads "like an icon"; only *then* does a human look, and only at what a machine cannot judge — does this solve the right problem, does the design hold, is the trade-off acceptable. The principal-level battle is judgement, not counting. <span style="color:red">*"Don't review as a senior anymore — build the fences as a senior"*: the senior's leverage moves from reading diffs to writing [[guardrails]] — hooks, [[archunit-drift-control|ArchUnit]], [[codeowners-elders|CODEOWNERS]], sandbox ([[2026-05-13-ai-it-services]]).</span>

**Skip trivial diffs.** A renamed-field unit-test edit is zero brain but costs attention — the scarcest resource. Encode "don't even look at that" in a review skill. More broadly, stop reviewing agent-written unit tests: they are tightly bound to the implementation, cover only the cases you anticipated, and add no information. Review the [[acceptance-test-bdd|acceptance tests]] instead. <span style="color:red">The test-quality criterion: read it aloud to a PO and they say "yes, that's what I want" — *"when `repo.findById` then return list"* can't be read to anyone ([[2026-08-06-ai-bank]]).</span> And celebrate finding "option C" — independent judgement that the agent didn't offer beats rubber-stamping its "A or B." If you find a bug, your AI review pipeline did a lousy job; by the time the human looks, multi-model multi-pass review should have caught it.

<span style="color:red">What judgement still catches that green tests don't:</span>
- <span style="color:red">**Readability for humans.** On an AI-written nested-stream/flatMap/three-line-ternary mapper, a participant nailed it: *"AI wrote this the optimal way for **it** to understand — not for a human."* Encode readability rules (no `Stream` return types, ternaries only if they fit half a line). Clean code now matters for *your* bandwidth, not the agent's; and agentic engineering in a language you can't read is **blind review** ([[2026-04-07-ai-product-team]], [[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Over-delivery from training-data idioms.** An unrequested `Pageable` silently truncating a grid to 10 rows slipped past ≈20 people reviewing together. Anti-features must be explicit in the prompt (*"flat list, no paging, no truncation"*) — see [[non-goals]] ([[2026-05-06-ai-ecommerce]]).</span>
- <span style="color:red">**AI security smells.** For "simple" queries assistants hand-write JPQL/SQL with string concatenation (`createQuery("… " + name)`) — an injection vector; encode it as a Semgrep/CodeQL rule so the build fails ([[2026-05-18-secure-coding-payments]]).</span>
- <span style="color:red">**Schema and ORM consequences**: a new FK without `ON DELETE` (a vet with visits can no longer be deleted); an eager `@ManyToOne` → N+1 on every list, which the agent fixed only in the main finder ([[2026-09-02-ai-insurer]]).</span>
- <span style="color:red">**Patterns imitated where they make no sense** — e.g. per-call mutable state (27 fields) lifted into a pooled stateless bean with `initialize()`/`finalize()`, sharing state across calls ([[2026-07-06-ai-medical-diagnostics]]).</span>
- <span style="color:red">**"Bug fixed" in the UI only.** Attack the backend directly (a `curl` creating a visit in year 6 AD: pretty in the UI, hole in the backend), then make a permanent rule: *"every restriction is enforced first in the backend, then displayed nicely in the front-end"* — prevent the class, not the instance ([[2026-08-03-ai-telecom-ops-qa]]).</span>

## <span style="color:red">Defences before the human: "no green build, no review"</span>

<span style="color:red">The human sees code **last** — after hooks, a green build, the agents' argument and the diagrams ([[2026-09-30-ai-enterprise]]). The order that emerged across sessions:</span>

1. <span style="color:red">**Programmatic gates** — CI, Sonar/Fortify/PMD/Checkstyle for quality, Snyk for security, then **merge `main`** to prove the PR is mergeable. Review again after the agent "fixed" the build: in panic it comments out tests ([[reward-hacking]], [[ci-green-loop]]).</span>
2. <span style="color:red">**Adversarial AI review in a fresh context** — never ask the author *"are you sure?"* or *"review your own code"*: it always defends its work, and after ≈300k tokens of tool calls in one direction it is blind to its own output. Claude Code's native [`/code-review`](https://code.claude.com/docs/en/code-review) runs isolated sub-agents on different angles; `/simplify` spawns 3–4 single-purpose ones (simplify, reuse, efficiency, altitude). **The reviewer may be the same model — the essential thing is the separate context**; cross-family is a bonus (Codex "smells" Opus code and is harsher — see [[multi-model-review]]).</span>
3. <span style="color:red">**The human**, tagged only when the loop considers the PR perfect — maybe 8 hours later ([[agentic-review-pipeline]]).</span>

<span style="color:red">Making the AI reviewer worth its tokens:</span>
- <span style="color:red">**Give the reviewer the same inputs the coder had** — spec markdowns, ticket, tools, skills/plugins — or it reviews nothing ([[2026-09-04-ai-software-company]]).</span>
- <span style="color:red">**Start with one reviewer; split into specialists each time slop slips through** (clean code, reuse, performance, security, UI components, eventual consistency). Every miss becomes a rule in a markdown before it becomes a new agent. Reviewer-specific rules go in a dedicated `review.md`, not CLAUDE.md. Gotcha: the repo-root [`REVIEW.md`](https://code.claude.com/docs/en/code-review#review-md) is read by the **managed GitHub Code Review**, not by the local `/code-review` command.</span>
- <span style="color:red">**Never Haiku for review** — minimum Sonnet-high or Opus-medium ([[model-hierarchy]]).</span>
- <span style="color:red">**"Ask an agent for issues and there shall be issues."** Four agents told to find faults *will* find some; the main agent decides what to apply, a local auto-fix skill should fix only the obvious ones, and you re-read what was auto-fixed — otherwise **overfitting**. When author and reviewer disagree, it's usually a **spec gap**: the author took a hard design decision alone.</span>
- <span style="color:red">**High stakes**: run the same adversarial agent on the plan/architecture before you read it.</span>
- <span style="color:red">**Cost**: adversarial review runs **30–50% of the implementation cost**. Data points: a +661/−35 PR for $4.10; 4 Opus agents ≈$10–15; ≈$20 for a medium cross-model session. *"Throwing the dice twice catches ≈70% of issues"* is an in-room estimate, not a published figure.</span>
- <span style="color:red">Third-party layers exist: [CodeRabbit](https://www.coderabbit.ai/) as an automated reviewer; [BitDive](https://bitdive.io/) feeds runtime execution facts into PR review. A CI reviewer via `claude -p` is possible — but decide **on whose key** ([[headless-claude]]).</span>

## Review *sorted*, not top-to-bottom

A reviewer's attention dies roughly halfway through a big PR, so do not read it in file order. Run a [[multi-model-review]] quorum first and **surface the lines the reviewers disagreed on first** — spend your sharpest minutes where the bots fought, not on the boilerplate they all approved. "Review sorted, not selective" is the single trick that shrinks PR-review pain to ≈10% of the effort. For the spec, the analogue is **pair-reading**: 10 minutes each, alternate, stop when a brain dies ("you notice the other guy died → stop").

<span style="color:red">Sorting keys that work, in combination:</span>
- <span style="color:red">**Uncertainty-first** — the coder logs its assumptions with a confidence and the review starts from the lowest, plus the reviewer findings it rejected: the [[assumptions-ledger]]. The killer prompt: *"Present your work descending by where you disagreed with the reviewer, ignored its findings, or weren't confident — with images. You have 30 minutes until my brain dies."* The cheap retrospective opener: *"Are there any assumptions you made? Any questions you want to ask me about the context?"*</span>
- <span style="color:red">**Heat-first** — every reviewer remark or failed CI "heats up" a file; review descending by heat while you're still alert ([[bug-heatmap]]).</span>
- <span style="color:red">**High-signal artifacts first, code last** — dependency manifests (new deps are supply-chain events), `openapi.yaml`, `db-schema.md`, C4/package diagrams, sequence diagrams of critical flows; reviewing 30 lines of generated YAML beats 800 lines of Java ([[review-by-images]], [[deterministic-diagrams]]). The autumn version is **outside-in: video → spec → tests → API → data**, with the Playwright [trace viewer](https://playwright.dev/docs/trace-viewer) and trace-generated sequence diagrams as the backend X-ray ([[2026-05-11-ai-software-vendor]], [[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**When you no longer read everything**, look *here and there*: complex logic on critical flows, the acceptance tests, big design decisions (which microservice, which cache, Kafka vs polling), **where the agent tells you to look** — then play with the app with common sense ([[2026-08-13-ai-insurer]]).</span>

<span style="color:red">This ordering is rendered by a script, not hallucinated: Victor's [human-review](https://github.com/victorrentea/human-review) report runs at **0% AI**, maps every acceptance criterion and PR file to the tests that traverse it (**red = no test**), and opens file+line in the IDE — see [[human-review-dashboard]]. A full guided review of the demo cost $3.79. *AI's best job is building your own tooling* — that tool was vibe-coded and never read ([[2026-09-25-ai-bank-migration]]).</span>

## "Done" includes "I watched it work"

The coding agent should not even *request* review until a [[sub-agents|sub-agent]] has **driven the running app** — deployed to a dev environment, clicked through with [Playwright](https://playwright.dev), screenshotted, written and run the [[acceptance-test-bdd|acceptance tests]] in the browser. Driving the app is common sense, not the review itself; it just means the review bots and the human are critiquing something already shown to run, not guessing. <span style="color:red">Pre-deploy an environment **per branch** — ideally a live Docker stack of the branch booting in seconds, opened from your phone — and show the **cost per phase in dollars** next to the generated diffs ([[2026-09-18-ai-agentic-how]]). An OpenAPI diff tool proves there is no breaking change between the two specs (optional fields in a *request* are non-breaking; in a *response* they can break strict clients).</span>

## The PR-size sweet spot and the attention budget

Granularity has two failure modes. **Too big** — a 1000-line end-of-sprint dump easily hides a bug, and your attention runs out before the end. **Too small** — six tiny parallel branches collide on merge, producing rework and bugs when they fight, and you won't have the patience to babysit each one. Aim for the middle (fifteen ≈200-line PRs over one 3000-line one); for the unavoidably-big ones, fall back to sorted review and assume your brain dies halfway. A quick rule: **+1,500 lines is a declaration of war** — and a one-line vague ticket without context is *also* a declaration of war. Slice monster features into ≈2 iterations.

<span style="color:red">**The stable number is time, not lines: cap any review at ≈45 minutes**, in one uninterrupted session, in the morning (adult attention span ≈40–45 minutes). The line budget that fits varies with the quality of the guided-review aids — ≈500 lines bare, ≈1,000 lines with artifacts and a "read this first" guide; **3,000 = dead**. At half-time take a real break. Good windows: the hour before lunch or 15:00 → end of day, never the post-lunch slump; not done by 17:00 → let it sink overnight. In a solo *markdown* review, brain death starts **around line 15** — read in turns with another human ([[2026-09-07-ai-bank]], [[2026-09-14-ai-industrial]], [[2026-08-03-ai-telecom-ops-qa]], [[2026-07-30-ai-safety-critical]]).</span>

<span style="color:red">The 2026 problem is that **agent PRs are ≥1,000 lines** (lots of tests and docs) and rarely go below ≈500 even when stacked; AI edits layer by layer, so commit order isn't the natural reading order ([[2026-08-27-ai-it-services]]). Size heuristic: *under 100 lines → 10 issues found; over 300 → "all good"*. Countermeasures:</span>
- <span style="color:red">**Make the agent shrink its diff** — ask for minimised changes, even in documents. Beyond ≈1,000 lines you have a **scoping** problem, not a tooling one: slice by [feature](https://www.humanizingwork.com/the-humanizing-work-guide-to-splitting-user-stories/).</span>
- <span style="color:red">**[[stacked-prs|Stacked PRs]]** of ≈200 lines, approved one by one with fixes cascading forward — they let the reviewer *feel progress*. Sources disagree on the cost: one late-September session found that stacking "fakes progress — one fix ripples through the stack". Stacking helps size; rework still ripples. Alternative: one big PR whose history the agent rewrites into clean per-stage commits.</span>
- <span style="color:red">**[Conventional commits](https://www.conventionalcommits.org/en/v1.0.0/)** make a large diff readable by intent (feat/refactor/docs/chore); a git hook can reject commits without a real ticket number.</span>
- <span style="color:red">**Separate preexisting bugs from PR regressions** — a review markdown with git-history proof links showed a resource leak existed identically on `main`. Fix those on `main` in small PRs, then merge `main` *backwards* into the big branch; a latent bug too big to fix inline → file a ticket ([[2026-07-06-ai-medical-diagnostics]]).</span>

## <span style="color:red">Who reviews: the second pair of eyes moves beside you</span>

<span style="color:red">Classic async four-eyes review **is dead with agents** if the second human, absent from the analysis, just runs their own Claude and approves. Variants of the anti-pattern:</span>
- <span style="color:red">**"Reality now"**: a dev ships 1,000 lines, the reviewer runs their own AI and pastes its findings into PR comments — *"I want your brain, not your tokens."*</span>
- <span style="color:red">**The hopeless second reviewer**: a senior who never joined planning gets 1,000 commits and no artifacts explaining the train of thought — *"zero chance of survival."*</span>
- <span style="color:red">**Group review as an illusion of safety**: ≈20 people missed the `Pageable` above. A diff needs a single owner who reads every line.</span>

<span style="color:red">The healthy version: **pair programming is the review**. The same colleague who was with you at plan and implementation signs off; work is thinking now, not typing, so the pair joins **from the start**. On real agentic projects this costs slightly more but gave **zero defects at UAT**. Those who can't stand the closeness review the **spec markdown** asynchronously, not the code. A third pair of eyes only for grave things — guardrails, security, workflows — enforced by [[codeowners-elders|CODEOWNERS]]. (The Hawthorne effect sometimes cited for pairing is largely debunked — Levitt & List, 2011 — the argument stands without it.) The full ladder: zero → guided → one agent → specialised agents → second human → six eyes ([[2026-08-27-ai-it-services]], [[2026-09-02-ai-insurer]], [[2026-09-16-ai-safety-critical]]).</span>

<span style="color:red">Practical habits:</span>
- <span style="color:red">**Never sit alone in front of an Opus diff** (*"you are a Sonnet when you review with an Opus"*): pair, mob, or open a second terminal whose only job is to explain the diff. Keep a scratch agent open beside you so every hunch becomes a spawned investigation instead of a sidetrack.</span>
- <span style="color:red">**Warm up your brain 5–10 minutes** forming your own model *before* firing the agent, so you can judge its plan ([[skill-erosion]]).</span>
- <span style="color:red">**Someone else's agent-written PR**: don't review line by line — they didn't read it either. Merge locally, run `/code-review` + `/simplify`, then ask the agent what looks dubious. ([Entire.io](https://entire.io/) commits the agent *conversation* to git alongside the code.)</span>
- <span style="color:red">**Mob review with "stop the line"**: if one junior says *"what's this?"*, review stops and the group learns it together — training paid from review time ([[mob-spec-review]]).</span>
- <span style="color:red">**Tech-lead move**: ask the developer three specific questions about the generated code; they must be able to defend every line. The agentic **job interview** is the same: paste the problem into the agent in front of the candidate and ask them to review its solution.</span>

## <span style="color:red">Gradual trust: how much to keep reading</span>

<span style="color:red">Trust is earned statistically, per code area. The convergent rule: review carefully until **≈20 consecutive impeccable PRs** in one area, then read the 21st halfway, then 25% → 10% → 5% spot-checks. Several real teams sit at ≈50% of PRs reviewed, riskiest first, dropping to 40% and 30% when months pass without findings; reviewing the top ≈50% by criticality is a legitimate steady state. You end up guarding **≈5% of the code** with your own eyes; **merge into dev stays strictly human** however many impeccable PRs. Uncle Bob publicly [stopped reviewing agent code](https://x.com/unclebobmartin/status/2044114698451476492) in favour of measuring coverage, coupling and complexity; Victor didn't. See [[gray-factory]] and [[agentic-corporation]] for where this road ends ([[2026-08-10-ai-consultancy]], [[2026-08-27-ai-it-services]], [[2026-08-31-ai-software-vendor]], [[2026-09-07-ai-bank]]).</span>

## Proof-of-life merge gate

Nothing merges without a human proof-of-life gate — "rotate your face to the right, are you alive?" The final approval is the one step that cannot be automated away. Persist the review verdict as a **committed `.md`** in the PR so it's durable, searchable, and survives conversation resets. See [[agentic-review-pipeline]] for the full ordered pipeline that precedes the human gate.

## Auto-fix CI on push

A git hook makes Claude **watch its own build after push and fix it if it breaks** — *"don't you dare push without watching the build."* The hook notifies the agent of the push, the agent monitors CI, and if the build fails it diagnoses and re-pushes a fix. This removes the "push and forget" trap. See [[ci-green-loop]] for the two variants and [[reward-hacking]] for the defenses against a cheating agent that makes CI green by deleting tests. <span style="color:red">Put CODEOWNERS on workflows and hooks so **the agent can't dismantle its own protections unseen**.</span>

## Never approve what you don't understand

The human rule underneath all of it: **if you don't understand something, stop** — even when you're reviewing alone, never approve what you can't explain. This is why you [[skill-erosion|front-load the analysis]] (a spec agglomerates ≈7 days of decisions into ≈1 hour) and why you review the [[acceptance-test-bdd|acceptance tests]] rather than the AI's mock-coupled unit tests, which are worthless to read.

<span style="color:red">**Fatigue → LGTM → approve** is *the* reason agentic projects fail, and the risk of the year. *"LGTM on AI slop without reading = looking for a new job next year"* — if you sign the PR, you read it. The subtle version: reading an agent's text, thinking *"sounds good"*, and not reading it word by word — it proposed sorting by `last_name` while the grid had a single combined `Name` column; a 300-line plan silently assumed the wrong library at line 122 that nobody reached ([[approval-fatigue]]). If you don't understand, ask: a cheap internal chatbot first, then 15 minutes, then the senior next to you — and **dig in a new conversation**, so the working agent doesn't lose its task thread. Saying *"I'm at question 10 of 40"* at stand-up is fine; otherwise you stop owning the solution. An architect agent that outruns you gets asked to **explain**, not LGTM'd ([[2026-08-06-ai-bank]], [[2026-09-04-ai-software-company]], [[2026-09-07-ai-bank]]).</span>

## <span style="color:red">When slop gets through: fix the markdown</span>

<span style="color:red">*"All the heavy work happens in the markdowns. If you see dirt at code review, you failed"* — *"better an hour in markdown than 3 hours in code review."* More than half of the reported AI garbage traces back to a line in a design markdown nobody read; 4 times out of 5 the escape is our fault (blind approval, contradictory requirements). So for any garbage that reaches you, ask **which agent or markdown missed it** and fix *that* — a line in AGENTS.md, a review hint, a plugin skill, a guardrail, a specialist reviewer ("upgrade the tower", tower-defence style; see [[self-improving-knowledge]]). Victor's human-RL loop: get deliberately outrageous about a style issue, fix it, *then* feed the rule into the frontend `CLAUDE.md`. The Boy Scout version: when review surfaces slop (a list endpoint returning the full detail DTO), fix it and add a two-line steering rule.</span>

<span style="color:red">**Verify the patch clean**: re-run the same work in a new session (`/clear`) *without telling it what you added* — otherwise the fix is in context and you only confirm it obeys ([[2026-08-27-ai-it-services]]). And demand **a failing test that shows the bug**: *"that's how the elders do it"* doesn't pass review. To convert sceptics, give Opus a PR you were proud of a month ago.</span>

## See also
- [[multi-model-review]]
- [[static-analysis-gauntlet]]
- [[sub-agents]]
- [[acceptance-test-bdd]]
- [[skill-erosion]]
- [[spec-driven-development]]
- [[agentic-review-pipeline]]
- [[mob-spec-review]]
- [[ci-green-loop]]
- [[reward-hacking]]
- <span style="color:red">[[assumptions-ledger]]</span>
- <span style="color:red">[[human-review-dashboard]]</span>
- <span style="color:red">[[bug-heatmap]]</span>
- <span style="color:red">[[stacked-prs]]</span>
- <span style="color:red">[[codeowners-elders]]</span>
- <span style="color:red">[[gray-factory]]</span>
- <span style="color:red">[[approval-fatigue]]</span>
- <span style="color:red">[[review-by-images]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-26-ai-agentic-how]]
