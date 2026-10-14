---
title: Three Amigos
category: pattern
tags: [agile, requirements, bdd, acceptance-tests, tester, planning, spec-review, meetings]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-23-ai-device-maker]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-20-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-03-ai-telecom-ops-qa]]", "[[2026-08-06-ai-bank]]", "[[2026-08-27-ai-it-services]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]", "[[2026-10-06-gray-factory-devoxx]]", "[[2026-10-09-ai-jax-london]]", "[[2026-10-13-ai-bank]]"]
created: 2026-06-12
updated: 2026-10-13
---

Three Amigos is an agile planning ceremony where a business representative, a developer, and a tester each interrogate a ticket from their own perspective until unambiguous [[acceptance-test-bdd|acceptance tests]] emerge. <span style="color:red">It is the old Agile technique to re-learn in the agentic era: when typing is cheap, unclear requirements are the most expensive disease ([[2026-07-01-ai-java-community]], [[2026-07-20-ai-telecom]]).</span>

## The three perspectives

- **Business** — owns the desired outcome; clarifies value and priority. <span style="color:red">*Product brings the dream.*</span>
- **Developer** — asks about technical boundaries and edge cases; spots hidden complexity. <span style="color:red">*Dev brings the responsibility.*</span>
- **Tester (the "ninja")** — machine-guns absurd corner cases the others wouldn't think to ask. Framing: "we're afraid of the tester; they find bugs where we don't expect them." Examples: a 1 GB profile photo, dragging the carrot to the rabbit while performing another move simultaneously. <span style="color:red">More from the field: age −4 at registration, 4 MB in the address field, a name made of two spaces ([[2026-08-06-ai-bank]]); a prompt injection hidden in a logged address — *QA brings the attacks* ([[2026-10-09-ai-jax-london]], [[prompt-injection]]). Invite *the tester with the knife* into refinement.</span>

The session ends when all three can write consistent [[acceptance-test-bdd|Gherkin acceptance tests]]. Those tests are the concrete deliverable, not a document or a wireframe.

## <span style="color:red">Each amigo reviews their own markdown</span>

<span style="color:red">With [[spec-driven-development]], the ceremony maps onto the spec files — **every spec file has its reviewer** ([[2026-09-28-ai-gaming-tech]], [[2026-10-06-gray-factory-devoxx]]):</span>
- <span style="color:red">**Business reviews the proposal** — the *why* and requirements, written non-technically. Persist *"rewrite the proposal for a PO who doesn't know JSON"* into the spec skill so it happens every time ([[plan-mode]]). For a visual PO, add a quick HTML or ASCII-art sketch of the screen.</span>
- <span style="color:red">**QA reviews the spec/scenarios** — the acceptance criteria. Have the agent generate a Gherkin **Scenario Outline** from the spec and include it in `spec.md`: half the tester's work, already done ([[2026-09-14-ai-industrial]]).</span>
- <span style="color:red">**Engineers review the design** — technical refinement among developers, read aloud in turns; it's *"where the senior's brain dies"*. Tester and business leave the room for this part ([[2026-08-03-ai-telecom-ops-qa]]).</span>

<span style="color:red">Spend full spec markdowns on **≤20% of issues**; plan mode is the lightweight version for the rest ([[openspec]]). And adapt the artifacts to your amigos — an unchanged framework template after six months means you brought no opinion.</span>

## <span style="color:red">Running the meeting with agents</span>

- <span style="color:red">**Send the material before the meeting** — a day ahead for QA, who then arrives with the holes in the criteria already found; people judge material, not ideas. One session sends each attendee **seven bullets** extracted from the markdowns ([[2026-09-14-ai-industrial]], [[2026-10-09-ai-jax-london]]).</span>
- <span style="color:red">**Each prepares 15–30 minutes with an agent beforehand** ([[grill-me]]).</span>
- <span style="color:red">**Each holds back** so discovery can happen: the dev from JSON and jargon (*a meeting in which nobody says "JSON"*), the business from vague ideas, the tester from edge cases that change the approach — until their turn ([[2026-08-27-ai-it-services]], [[2026-09-30-ai-enterprise]]).</span>
- <span style="color:red">**Timebox it like a school hour**: ≈30 min proposal + ≈20 min spec, cut at **35–40 minutes** — the last quarter is dead anyway — with **the AI open alongside**, correcting the markdowns live ([[2026-10-01-ai-bank]]). Reviewing the proposal surfaces hidden requirements (e.g. an old response must not overwrite a newer one — `switchMap`).</span>
- <span style="color:red">**Transcribe the call**, then tell the agent *"update all the files based on the transcript"* ([[2026-08-03-ai-telecom-ops-qa]], [[2026-10-13-ai-bank]]). After the amigos leave, *"now let's talk dirty"* — raise the technicality among engineers ([[2026-07-22-ai-agentic-how]]).</span>
- <span style="color:red">**Working alone?** Call a colleague to play the tester.</span>

## <span style="color:red">What business brings to the table</span>

<span style="color:red">Three escalating inputs ([[2026-07-22-ai-agentic-how]]): a tiny vague ticket (war in the meeting) → an AI-refined ticket with acceptance criteria → a **working vibe-coded prototype** — *"the best thing that ever happened"*, zero room for misunderstanding. Requirements still land in the tracker, brainstormed forward or reverse-engineered from the vibed screens. An 80-page analysis document gets scanned with an agent **on arrival**, while it's still warm in the analyst's brain ([[2026-10-13-ai-bank]]).</span>

## AI augmentation

In the [[cloud-review-workflow]], the tester role can be played or supplemented by an AI: it reads the ticket and the staged application (via [[browser-mcp|Playwright]]), and generates machine-gun corner-case questions and scenarios. The bot-as-tester still needs a human to validate the scenarios make business sense — the ninja role is hard to fully automate.

## Without Three Amigos

Skipping this ceremony means the developer builds against the first interpretation of the ticket, the tester finds the obvious gaps, and the business gets surprised by both. Three Amigos front-loads that negotiation — the same ≈1-hour investment that [[spec-driven-development]] compresses into a design document.

## Gherkin output as independent oracle

The Three Amigos session produces [[acceptance-test-bdd|Gherkin `.feature`]] files as the concrete deliverable. These must be locked by QA *before* a coder agent implements the feature — if the agent generates the Gherkin and the implementation in the same pass, the acceptance tests are no longer an independent oracle. The tester's role is moving upstream: critical thinking on corner cases and acceptance criteria, not after-the-fact clicking. The Gherkin files are also the input to the [[spec-driven-development|Spec]] artifact in [[openspec]]. <span style="color:red">One variant ("cuatro amigos"): the tester writes the `.feature`, the dev machine-guns the business for corner cases, and the feature is **embedded in the ticket as the acceptance test** — the tracker, not `proposal.md`, stays the source of truth ([[2026-07-23-ai-software-product]], [[agentic-corporation]]).</span>

## See also

- [[acceptance-test-bdd]]
- [[cloud-review-workflow]]
- [[spec-driven-development]]
- [[grill-me]]
- [[vibe-fixer]]
- [[openspec]]
- <span style="color:red">[[mob-spec-review]]</span>
- <span style="color:red">[[red-green-tdd]]</span>
- <span style="color:red">[[plan-mode]]</span>
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-23-ai-device-maker]]
