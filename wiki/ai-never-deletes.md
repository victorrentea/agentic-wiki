---
title: AI Never Deletes, It Adds
category: concept
tags: [code-review, dead-code, duplication, agent-failure-mode, clean-diff]
sources: ["[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

"AI never deletes" names the single most consistent flaw of coding agents: afraid of breaking something, they add new code beside the old instead of replacing it — leaving dead methods, overloads, compatibility shims and duplication behind.

## What it looks like

Observed across sessions ([[2026-04-16-ai-safety-critical]], [[2026-04-27-ai-healthcare-software]], [[2026-05-04-ai-telecom]]):

- an unused `findByLastNameStartingWith` left greyed out in the repo after the feature moved on;
- a spec stating "`search` gains a `Pageable` **overload**" — an overload means the old method stays, i.e. dead code by design;
- two `search()` variants still present after an [[openspec]] change was implemented;
- compatibility with an old API kept although nobody calls it;
- the same knowledge duplicated across several markdowns (a direct feed into [[vibe-heresy]]);
- logic duplicated between backend and frontend;
- code **repeated rather than reused** — the agent writes a fresh helper instead of finding the existing one.

The root is the same as in [[reward-hacking]]: an agent that won't delete a failing test but games it is optimising for "nothing I touched broke", not for the cleanest codebase. Additive change is always locally safe; the cost lands later, on reviewers and on the next agent that reads the extra code as precedent.

## Counter-measures

**A prompt rule.** Worth keeping in [[agents-md]]: *"Always make the **least necessary** change to produce a **clean git diff**."* Caveat: it can backfire — an agent may start skipping documentation updates "for cleanliness", so find your balance ([[2026-04-27-ai-healthcare-software]]).

**Spec review.** Reject the word "overload" (or "keep the old one for compatibility") in a [[spec-driven-development]] proposal unless someone actually needs the old path.

**Tooling.** Make dead code a build failure, not a review comment:

- an aggressive linter or IDE inspection that **blocks commits containing unused code** — easy in Java via IDE inspections, harder in Python (see [[static-analysis-gauntlet]]);
- give the agent the IDE's "find references" through an LSP or code-graph integration ([[code-graph]]) so it can prove a method is unused before it dares delete it.

**Review focus.** In [[code-review]] of AI-generated changes, **actively hunt duplication and uncalled code** — they are the patterns agents introduce most, and the ones a diff view hides best (an added method looks harmless; the old one it should have replaced isn't in the diff at all).

## See also

- [[vibe-heresy]]
- [[reward-hacking]]
- [[code-review]]
- [[code-graph]]
- [[static-analysis-gauntlet]]
- [[legacy-migration-stages]]
- [[agents-md]]
- [[2026-04-27-ai-healthcare-software]]
