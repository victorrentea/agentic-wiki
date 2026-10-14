---
title: Figma MCP
category: tool
tags: [mcp, figma, design, ui, mobile, design-to-code]
sources: ["[[2026-06-23-ai-device-maker]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-07-03-ai-bank]]", "[[2026-07-23-ai-software-product]]", "[[2026-08-27-ai-it-services]]"]
created: 2026-06-23
updated: 2026-10-13
---

The Figma MCP lets an agent pull design specs straight from Figma into the app it's building — *if* the screens are genuinely maintained in Figma, this is "paradise."

## What it enables

Wired up over [[model-context-protocol]], the agent reads the actual design source — component layout, spacing, colours, typography — and generates UI code against it, instead of guessing from a prose description or a flat screenshot. The hard precondition is the catch: it only works when the design is *truly* kept current in Figma. *"Paradise — you don't know how lucky you are."* If the Figma file has drifted from the live app, the agent faithfully implements the wrong thing.

<span style="color:red">Where it earns its keep is **propagating design *changes* across many screens**, not one-off mockups ([[2026-07-23-ai-software-product]]). There is also a lower-tech route: drop the exported Figma zip into the repo and the agent recognizes and applies it; a Figma link in the ticket means the screens *"come to you designed already"* ([[2026-07-03-ai-bank]]).</span>

## Is AI bad at design?

<span style="color:red">Earlier advice in these sessions was a standing rule — **AI is bad at design**: *"don't ask AI for fancy design; that's why we still need Figma."* By spring 2026 prompt-to-design tools had changed the picture ([[2026-04-16-ai-safety-critical]], [[2026-04-27-ai-healthcare-software]], [[2026-05-04-ai-telecom]]):</span>
- <span style="color:red">[Stitch](https://stitch.withgoogle.com) (Google) *"replaces 6 months of Figma iterations with 5 minutes — for prototypes"*.</span>
- <span style="color:red">[Figma Make](https://www.figma.com/make/) (sketch or template → prompt → HTML) — one UX designer's verdict: *"Figma died when this came out"*. Lovable was rated worse.</span>
- <span style="color:red">UX designers reportedly tell Victor AI now designs better than they do ("the death of CSS").</span>

<span style="color:red">The current split: for a **real design system**, stay on Figma + MCP so the human owns the system and the agent owns the mechanical translation to code; for **fast UI exploration**, Stitch / Figma Make win. They are complementary. One team went further: UI mock-ups are now made directly in code, and Figma remains only for the picture shown to the client, not for iterating ([[2026-08-27-ai-it-services]]).</span> Keep a human on colour and UX either way. The Figma MCP is the design-side counterpart of [[adb-automation]] (which closes the loop on the *running* UI) and pairs with the [[feedback-loop]] discipline: design in, code out, screenshot-compare until they match.

## UX polish is still human

<span style="color:red">The same team does the final UX pass **live, by voice, with the client in the room** — a change every 5 seconds ([[wispr-flow]]). The defects only a human noticed: column widths jumping on re-sort, a sort icon invisible until clicked, variable row height, "previous/next" as text instead of arrows. Respect the user who has 15 years of muscle-memory clicks in the old screen ([[2026-08-27-ai-it-services]]).</span>

A useful tip when there is *no* Figma source: **draw the UI** instead of describing it — a hand-drawn wireframe pasted as an image is more token-efficient and less ambiguous than prose (an Anthropic training tip). See [[wispr-flow]] for dictating the surrounding ticket.

## See also
- [[model-context-protocol]]
- [[adb-automation]]
- [[feedback-loop]]
- [[browser-mcp]]
- [[wispr-flow]]
- <span style="color:red">[[review-by-images]]</span>
- [[2026-06-23-ai-device-maker]]
