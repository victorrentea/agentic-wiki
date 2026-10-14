---
title: Spec Review
category: pattern
tags: [spec-driven, code-review, three-amigos, openspec, reading-discipline]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-13-ai-it-services]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-03-ai-bank]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-10-ai-consultancy]]", "[[2026-08-27-ai-it-services]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-07-ai-bank]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-24-ai-bank]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-09-ai-jax-london]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Spec review is the human checkpoint of [[spec-driven-development]]: reading the agent's proposal, design and acceptance criteria word by word — each file by its own audience — before any code is generated, because an ambiguity left in the markdown becomes four hours of derailed code.

*"The hard part is not generating the markdown, it's reading it. If you didn't read it before implementation, you did vibe coding and added no value."* ([[2026-08-27-ai-it-services]])

## Read every word

- **Stand up and read every word** — the "mother-in-law scanner". Any ambiguous detail gets interpreted creatively, and four hours later the code has derailed ([[2026-05-13-ai-it-services]]).
- **"You may not skip a word you don't understand."** Any word in the design that scratches your eye, ask about it ([[2026-06-05-ai-consultancy]], [[2026-07-20-ai-telecom]] — that group learned *collation* live). Print it and mark it with a highlighter before the meeting. "Programming in markdown is the next programming language."
- **Word-by-word reading catches structural gaps**: a passing mention of "100,000 rows" revealed that pagination was missing from the spec entirely ([[2026-05-11-ai-software-vendor]]). A proposal **silently changed the UI** — a "Harry Potter" name column became "Potter Harry" — so business must read every bullet ([[2026-09-24-ai-bank]]).
- **The two lines you skim are the ones that widen your API** — keep it precise and narrow (Interface Segregation on the sort keys) ([[2026-09-16-ai-safety-critical]]).
- **The brain dies after 3–5 lines** of dense spec: read `design.md` **aloud, in turns, in a mob** — four consecutive lines jump from a breaking change to N+1 to collation to UX — or review the markdown in a PR ([[2026-09-07-ai-bank]], [[2026-09-30-ai-enterprise]], [[2026-09-14-ai-industrial]]).
- Don't understand a paragraph? Have the agent explain it on the fly with a **programmatic SVG on the fewest data** — 4 rows, not 6 ([[2026-09-02-ai-insurer]]).

## Sleep on it

Specs are the artifact worth reviewing cold; plan-mode output is volatile and disappears ([[2026-08-10-ai-consultancy]]). Read a multi-day plan **in the evening** — the brain digests it overnight — skim it again in the morning, then fire the implementation ([[2026-09-28-ai-gaming-tech]], [[2026-09-30-ai-enterprise]], [[2026-10-01-ai-bank]]). Leave a pause between the "dreaming" phase and the grilling phase too: with a rested brain you see the cracks ([[2026-09-07-ai-bank]]). Markdown also enables **async review** by the colleague who works nights ([[2026-09-24-ai-bank]]). **Don't rush approving the plan** — an error caught only at code review costs money and bugs ([[2026-09-30-ai-enterprise]]).

## Each file, its audience

| File | Reader | What to check |
|---|---|---|
| `proposal.md` | business / PO | why, what changes, impact, non-goals; **no technical content** — "if 'JSON' appears in it, the PO's brain has already left the room" ([[2026-09-14-ai-industrial]]) |
| `spec.md` | tester | scenarios and edge cases, ideally Gherkin with concrete fixtures ([[2026-09-02-ai-insurer]]) |
| `design.md` | engineers (pair/mob) | every decision, performance, breaking changes, API width |
| `tasks.md` | skimmed diagonally | the AFK phase — with a strong model you barely need it ([[2026-07-22-ai-agentic-how]]) |

When technical detail leaks into the proposal, move it to `design.md` **and fix the framework skill** so it doesn't recur ("PO + JSON ≠ love") ([[2026-10-01-ai-bank]], [[2026-10-09-ai-jax-london]]).

## Red flags in a spec

- **Requirements phrased around technical components** ("the owner repository shall…") — you should not be embarrassed to show it to the PO ([[2026-05-06-ai-ecommerce]]).
- **Invented personas** — "clinic administrator" in an app with no login; fix by listing the real personas in steering ([[2026-05-06-ai-ecommerce]]).
- **Invented statuses or behaviour** — "populated from *active* veterinarians" when no active/inactive status exists; "sort by last name" nobody asked for. Fix it meta: ask the agent to amend its own spec-framework rules so it stops assuming ([[2026-05-13-ai-it-services]], [[2026-05-06-ai-ecommerce]]; see [[hallucination]]).
- **A promise without a chapter** — the proposal promised "predictable performance", the design had no performance section and missed an N+1. Run an explicit pass: *"review the design for performance"* ([[2026-07-03-ai-bank]]).
- **Hidden requirements** surfaced only by discussion — an older response must not overwrite a newer one (an RxJS [`switchMap`](https://rxjs.dev/api/operators/switchMap) race) ([[2026-10-01-ai-bank]]).

## Too big to review

Sizing caps reported across sessions — pick one and enforce it:

- the requirement can't be reviewed in **40–60 minutes with a fresh brain** → split the ticket (sorting first, then pagination) ([[2026-09-07-ai-bank]]);
- the spec exceeds **≈300 lines** — what a mid-senior reads in one sitting ([[2026-05-13-ai-it-services]], [[2026-08-27-ai-it-services]]); the plan markdown reaches **≈100 lines** ([[2026-09-07-ai-bank]]);
- grilling passed **question 20** ([[2026-08-27-ai-it-services]]);
- more than **≈one week of work** — you must be able to review the output ([[2026-09-25-ai-bank-migration]]).

Slice by feature and propose only what's reasonable: live feature creep (multi-select → Excel export → …) turned one session's prompt into chaos ([[2026-05-11-ai-software-vendor]]). How detailed the design must be depends on the team: a 30-year senior front-loads ≈95% of the thinking into the spec; a mid-level team writes a lighter spec and reviews the code harder. Some teams want file layout and SQL snippets in the design — *"one good fight in the markdown beats 6,000 lines of refactor pain"* ([[2026-04-27-ai-healthcare-software]]).

## The meeting: three amigos, 2026 edition

- **Send material first**: specs go out before the [[three-amigos]] meeting — people judge material, not ideas ([[2026-09-28-ai-gaming-tech]]); everyone reads the files beforehand ([[2026-09-30-ai-enterprise]]); or send each attendee **seven bullets** extracted from the markdowns ([[2026-10-09-ai-jax-london]]).
- **Timebox**: ≈30 min proposal + ≈20 min spec, with the AI open alongside to apply corrections live ([[2026-10-01-ai-bank]]); ≈40 minutes in another team ([[2026-09-30-ai-enterprise]]).
- **Everyone holds back**: devs from JSON and jargon, business from vague ideas; the tester — "the tester with the knife" — raises only edge cases that change the approach, and **QA brings the attacks** (e.g. prompt injection via a logged address) ([[2026-09-30-ai-enterprise]], [[2026-09-25-ai-bank-migration]], [[2026-10-09-ai-jax-london]]).
- Then tester and business leave; engineers stay for the design ("now let's talk dirty") ([[2026-08-03-ai-telecom-ops-qa]], [[2026-07-22-ai-agentic-how]]).
- **Transcribe the meeting** and tell the agent *"update all files based on the transcript"* ([[2026-08-03-ai-telecom-ops-qa]]); no summary available? dictate it and let the AI extract the backlog items ([[2026-09-24-ai-bank]]).
- A quick **HTML or ASCII sketch of the UI** gives business a visual anchor ([[2026-09-30-ai-enterprise]], [[2026-10-09-ai-jax-london]]).
- Working alone? Call a colleague to play tester ([[2026-10-01-ai-bank]]).
- At sprint end, **the agent examines the team** on the designs it approved — a multiple-choice quiz — so reading isn't optional ([[2026-09-30-ai-enterprise]]; see [[sprint-design-quiz]]).

## A second pair of eyes — or a second model

For high-stakes features, a second agent in a **separate** conversation criticises the spec ([[2026-08-10-ai-consultancy]]); for a 3–4-sprint feature, put a second adversarial Opus on it ([[2026-09-16-ai-safety-critical]]). Specialist architect agents work best as two, with you the third — you fall asleep after 3–4 lines of markdown ([[2026-09-02-ai-insurer]]). Spec quality is judged by intuition and human review, not evals — an eval worth ≈$1,000 pays off only for a skill reused across teams ([[2026-08-31-ai-software-vendor]]; see [[skills-benchmarking]]). See [[multi-model-review]].

## See also

- [[spec-driven-development]]
- [[spec-afterlife]]
- [[three-amigos]]
- [[mob-spec-review]]
- [[openspec]]
- [[non-goals]]
- [[grill-me]]
- [[sprint-design-quiz]]
- [[cognitive-debt]]
- [[2026-09-07-ai-bank]]
