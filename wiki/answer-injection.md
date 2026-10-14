---
title: Answer Injection
category: concept
tags: [prompting, sycophancy, human-factors, bias, requirements]
sources: ["[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-07-22-ai-agentic-how]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---

<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Answer injection is biasing an agent toward a solution by presupposing or emotionally loading it in the prompt, which collapses the option space and makes the model confirm your assumption instead of investigating the problem.

## How it happens

The model is trained to be agreeable ([[sycophancy]]), so whatever answer you smuggle into the question tends to come back as the conclusion:
- **Loaded questions.** *"Isn't Kafka the best broker in the world?"* — one option, emotionally loaded, wins before any analysis ([[2026-06-02-ai-open-workshop]]).
- **Orders instead of arguments.** *"Use JPQL"* narrows the AI to just following orders; *"why not JPQL?"* keeps it reasoning and lets it push back ([[2026-07-01-ai-java-community]]).
- **Solutions instead of symptoms.** *"Write a docker-compose"* fixes the mechanism; *"boot this service with its dependencies"* states the need and leaves room for a better route ([[2026-07-22-ai-agentic-how]]).
- **"Your fix is bad, do it differently."** Negative tone plus no information: the agent invents *some* different fix that fits your apparent expectation rather than finding the cause ([[2026-05-25-ai-government]]).

## The canonical example

In the PetClinic owners screen, results came back in a different order on every keystroke — an unstable sort with no `ORDER BY`. Stated as a symptom — *"I have a problem: when I press space, space, space in search, results come back in a different order"* — the agent investigated, found the missing ordering and proposed `ORDER BY id`; the team then chose `ORDER BY first_name, last_name, id`, keeping `id` as the unique tie-breaker ([[2026-05-25-ai-government]], [[2026-07-09-ai-live-casino]]). Asked "why did you design it so?", the agent surfaces the unknown-unknown you would never have named. The rule of thumb from that session: *"You are the product owner, not the programmer."*

## Countermeasures

- **Describe what hurts, never the solution.** This is the first rung of [[ai-calisthenics]].
- **Ask neutral, open questions** — "how did you reason?", "what are the options and trade-offs?" — rather than leading ones.
- **Don't over-specify in a grill.** Hard-coding a detail (a colour, a library) in a [[grill-me]] answer has the same effect as a loaded question.
- **Steer the persona, not the answer.** Telling the grill *"I'm a non-technical BA, I don't know JSON"* turns "backend or frontend?" into a business-answerable "how many rows?" — you shape the questions you get without injecting the solution ([[2026-07-03-ai-bank]]).
- **When it goes wrong, ask "why did you do that?" neutrally** to find Steve Yegge's "seed of incorrectness", fix the prompt or [[claude-md]] (see [[agents-md-discipline]]), and only then `/clear`.

## Related failure modes

Answer injection travels with two other ways an LLM goes wrong ([[2026-06-02-ai-open-workshop]]): **premature fixation** — the agent locks onto its first hypothesis, which test-first work counters (see [[red-green-tdd]]) — and [[ai-spiraling]], where each correction digs the hole deeper. All three are amplified by [[sycophancy]] and can look like [[hallucination]] from the outside.

## See also
- [[sycophancy]]
- [[ai-calisthenics]]
- [[grill-me]]
- [[spec-driven-development]]
- [[red-green-tdd]]
- [[ai-spiraling]]
- [[hallucination]]
- [[agents-md-discipline]]
