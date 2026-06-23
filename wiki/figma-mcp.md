---
title: Figma MCP
category: tool
tags: [mcp, figma, design, ui, mobile, design-to-code]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-23
updated: 2026-06-23
new_in: 2026-06-23
---

<span style="color:red">🆕 New in Garmin 2026-06-23</span>

The Figma MCP lets an agent pull design specs straight from Figma into the app it's building — *if* the screens are genuinely maintained in Figma, this is "paradise."

## What it enables

Wired up over [[model-context-protocol]], the agent reads the actual design source — component layout, spacing, colours, typography — and generates UI code against it, instead of guessing from a prose description or a flat screenshot. The hard precondition is the catch: it only works when the design is *truly* kept current in Figma. *"Paradise — you don't know how lucky you are."* If the Figma file has drifted from the live app, the agent faithfully implements the wrong thing.

## Why it matters: AI is bad at design

The standing rule is **AI is bad at design** — *"don't ask AI for fancy design; that's why we still need Figma."* Keep a human on colour and UX. The Figma MCP is the bridge that lets the human own the design in their tool while the agent owns the mechanical translation to code. It is the design-side counterpart of [[adb-automation]] (which closes the loop on the *running* UI) and pairs with the [[feedback-loop]] discipline: design in, code out, screenshot-compare until they match.

A useful tip when there is *no* Figma source: **draw the UI** instead of describing it — a hand-drawn wireframe pasted as an image is more token-efficient and less ambiguous than prose (an Anthropic training tip). See [[wispr-flow]] for dictating the surrounding ticket.

## See also
- [[model-context-protocol]]
- [[adb-automation]]
- [[feedback-loop]]
- [[browser-mcp]]
- [[wispr-flow]]
- [[2026-06-23-ai-garmin]]
