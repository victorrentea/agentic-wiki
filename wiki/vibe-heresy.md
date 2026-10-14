---
title: Vibe Heresy
category: concept
tags: [vibe-coding, context, drift, spec, code-review, cleanup]
sources: ["[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-07-22-ai-agentic-how]]", "[[2026-07-23-ai-software-product]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-10-06-gray-factory-devoxx]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Vibe heresy is the fossil record of bad decisions an AI was allowed to write once — lingering in comments, test names, markdown and half-deleted files — that resurrects in tomorrow's prompts the way a heresy keeps resurfacing in a religion.

## Vibe coding vs vibe heresy

*"[[vibe-coding|Vibe coding]] is generating without reading. Vibe heresy is the fossil record of bad decisions vibe-coded yesterday that contaminate tomorrow's prompt."* The damage isn't the one bad line; it is the moment of inattention ("leave it like that") that lets the agent spread the mistake into comments, docstrings, markdown and code — and every one of those copies is context for the next feature.

## The mechanism

- **AI never deletes, it adds** ([[ai-never-deletes]]). A correction is a new layer on top; the old idea survives underneath in a comment, a test name, a stale paragraph of a spec.
- **The agent reads everything it finds.** A worked case from one team: a removed `last_name` search criterion kept reappearing in new specs, resurrected each time from leftover text.
- **Leftover markdown is the main reservoir.** One vibe-coded side project (≈$3,000/month of tokens) collapsed after ≈2–3 months as dead ideas kept climbing back out of old `.md` plans and notes — the agent could no longer tell the current design from abandoned ones ([[drift]]).
- **Self-justifying comments.** Agents leave comments explaining *why* they did what they did ("// kept for backward compatibility"). Those are arguments, not documentation — they defend yesterday's mistake to tomorrow's agent. Strip them at review ([[code-review]]).
- **Naming the wrong thing plants it.** Never write *"we removed X"* in [[claude-md|CLAUDE.md]] or a spec: the mention alone primes the model toward X. State only the current truth. One session linked this to Steve Yegge's "agent dementia"; the attribution was not checked.

## Defences

1. **Clean up the trail when you correct.** When you fix a derailed implementation, also delete the now-incorrect comments, docstrings, test names and doc paragraphs — the fix isn't done while the heresy is still written down anywhere ([[vibe-fixer]]).
2. **The radical one: revert and restart.** Revert everything, `/clear`, update the plan and CLAUDE.md, and regenerate. *"Pulling weeds one by one, the seed always survives in some comment; you have to dig and turn the soil."*
3. **Fix ideas in the spec before they spread** — the core argument for [[spec-driven-development]]: a wrong decision caught in a reviewed spec never reaches the code.
4. **Garden the docs continuously.** Old specs drift and derail agents, so a [[doc-gardener]] agent periodically rescans the markdown against the code and flags or deletes what no longer holds — a staple of the [[gray-factory]] setup.
5. **Find the root assumption** with the [[agents-md-discipline|seed-of-heresy Socratic loop]] — ask "why?" until the missing assumption surfaces, then encode the correct one.

## See also
- [[vibe-coding]]
- [[ai-never-deletes]]
- [[vibe-fixer]]
- [[drift]]
- [[doc-gardener]]
- [[spec-driven-development]]
- [[agents-md-discipline]]
- [[claude-md]]
- [[handover]]
- [[2026-04-27-ai-healthcare-software]]
- [[2026-05-04-ai-telecom]]
- [[2026-07-22-ai-agentic-how]]
- [[2026-07-23-ai-software-product]]
- [[2026-10-06-gray-factory-devoxx]]
