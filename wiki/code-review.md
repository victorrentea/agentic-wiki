---
title: Code Review
category: pattern
tags: [code-review, pull-request, judgement, sorted-review, attention]
sources: ["[[2026-06-11-ai-playtika]]", "[[2026-06-22-ai-kambi]]"]
created: 2026-06-11
updated: 2026-06-25
---


Code review in the agentic era is the human's last, scarcest gate: once agents write most of the code, reviewing it becomes one of the two real bottlenecks left (the other is writing the spec), so the whole craft is about spending a fading human attention budget where it changes the outcome.

## The human reviews judgement, not mechanics

It is degrading for a human to flag "this method is too long" or "too many parameters" when a machine can detect it. So everything mechanical is pushed onto the [[static-analysis-gauntlet]] (and [[tripwire|tripwires]]) until the code self-repairs and reads "like an icon"; only *then* does a human look, and only at what a machine cannot judge — does this solve the right problem, does the design hold, is the trade-off acceptable. The principal-level battle is judgement, not counting.

**Skip trivial diffs.** A renamed-field unit-test edit is zero brain but costs attention — the scarcest resource. Encode "don't even look at that" in a review skill. More broadly, stop reviewing agent-written unit tests: they are tightly bound to the implementation, cover only the cases you anticipated, and add no information. Review the [[acceptance-test-bdd|acceptance tests]] instead. And celebrate finding "option C" — independent judgement that the agent didn't offer beats rubber-stamping its "A or B." If you find a bug, your AI review pipeline did a lousy job; by the time the human looks, multi-model multi-pass review should have caught it.

## Review *sorted*, not top-to-bottom

A reviewer's attention dies roughly halfway through a big PR, so do not read it in file order. Run a [[multi-model-review]] quorum first and **surface the lines the reviewers disagreed on first** — spend your sharpest minutes where the bots fought, not on the boilerplate they all approved. "Review sorted, not selective" is the single trick that shrinks PR-review pain to ≈10% of the effort. For the spec, the analogue is **pair-reading**: 10 minutes each, alternate, stop when a brain dies ("you notice the other guy died → stop").

## "Done" includes "I watched it work"

The coding agent should not even *request* review until a [[sub-agents|sub-agent]] has **driven the running app** — deployed to a dev environment, clicked through with [Playwright](https://playwright.dev), screenshotted, written and run the [[acceptance-test-bdd|acceptance tests]] in the browser. Driving the app is common sense, not the review itself; it just means the review bots and the human are critiquing something already shown to run, not guessing.

## Proof-of-life merge gate

<span style="color:red">Nothing merges without a human proof-of-life gate — "rotate your face to the right, are you alive?" The final approval is the one step that cannot be automated away. Persist the review verdict as a **committed `.md`** in the PR so it's durable, searchable, and survives conversation resets. See [[agentic-review-pipeline]] for the full ordered pipeline that precedes the human gate.</span>

## The PR-size sweet spot

Granularity has two failure modes. **Too big** — a 1000-line end-of-sprint dump easily hides a bug, and your attention runs out before the end. **Too small** — six tiny parallel branches collide on merge, producing rework and bugs when they fight, and you won't have the patience to babysit each one. Aim for the middle (fifteen ≈200-line PRs over one 3000-line one); for the unavoidably-big ones, fall back to sorted review and assume your brain dies halfway.

## Never approve what you don't understand

The human rule underneath all of it: **if you don't understand something, stop** — even when you're reviewing alone, never approve what you can't explain. This is why you [[skill-erosion|front-load the analysis]] (a spec agglomerates ≈7 days of decisions into ≈1 hour) and why you review the [[acceptance-test-bdd|acceptance tests]] rather than the AI's mock-coupled unit tests, which are worthless to read.

## See also
- [[multi-model-review]]
- [[static-analysis-gauntlet]]
- [[sub-agents]]
- [[acceptance-test-bdd]]
- [[skill-erosion]]
- [[spec-driven-development]]
- [[agentic-review-pipeline]]
- [[mob-spec-review]]
- [[2026-06-11-ai-playtika]]
- [[2026-06-22-ai-kambi]]
