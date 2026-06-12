---
title: Vibe Coding
category: concept
tags: [agentic-engineering, anti-pattern, context-anxiety, product-owner, human-factors]
sources: ["[[2026-06-11-ai-playtika]]"]
created: 2026-06-11
updated: 2026-06-12
---


Vibe coding is generating software you never read — "forget the code exists" — the opposite end of the spectrum from disciplined [[agentic-engineering]].

## How long can you vibe?

Vibing works for a while, then rots. One test: ≈**3 months** of solo mania-pushing before it collapsed and engineering had to come back — diagrams, tests, decoupling. A machine signal of the same rot is **"context anxiety"**: past ≈600k tokens Opus becomes afraid to read more files, stops checking for duplication, and stops writing tests — a direct symptom of the [[dumb-zone]].

## The role shift

The durable trajectory is not "no engineering" but a **role shift**: developers become product owners / "managers of agents" who steer the big moves. Business may "vibe" a prototype for 2–3 days, then hand the rotted ship to engineers for de-shitification (a real, if crude, emerging job description). The antidote to vibe-coding's downsides is the whole rest of this wiki: [[spec-driven-development]], [[acceptance-test-bdd|acceptance tests]], [[multi-model-review]], and guarding against [[skill-erosion]].

<span style="color:red">**The [[vibe-fixer]] role** is the economic response to vibe coding at scale: an engineer receives ≈10,000 lines of almost-working output and applies judgement — what to keep, reuse, or discard — rather than rewriting from scratch. In the [[cloud-review-workflow]], business vibes a UI draft and the developer becomes the vibe-fixer. The leverage shifts from writing to judgement.</span>

## See also
- [[agentic-engineering]]
- [[dumb-zone]]
- [[spec-driven-development]]
- [[skill-erosion]]
- [[wispr-flow]]
- [[vibe-fixer]]
- [[cloud-review-workflow]]
- [[2026-06-11-ai-playtika]]
