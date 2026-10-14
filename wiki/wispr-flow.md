---
title: Wispr Flow
category: tool
tags: [dictation, voice, productivity, human-bottleneck, input]
sources: ["[[2026-06-11-ai-mobile-gaming]]", "[[2026-06-22-ai-sports-betting]]", "[[2026-06-23-ai-device-maker]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-04-ai-telecom]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-25-ai-government]]", "[[2026-06-05-ai-consultancy]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-20-ai-telecom]]", "[[2026-08-06-ai-bank]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-10-testing-ai-bank]]", "[[2026-09-24-ai-bank]]", "[[2026-10-07-agentic-reconversion-devoxx]]"]
created: 2026-06-11
updated: 2026-10-13
---

Wispr Flow is a voice-dictation tool that transcribes speech into any text field, used to remove the human typing bottleneck when driving agents.

## Why dictate

[Wispr Flow](https://wisprflow.ai) switches between languages mid-sentence (e.g. Romanian ↔ English) and recognizes technical jargon, so you can think aloud at speaking speed instead of typing. The deeper point: in agentic work **you are the bottleneck**, not the model. If a problem sits at the edge of your expertise, don't throttle your own bandwidth — think in your native language and let the tool translate. Dictation pairs with the "stop typing code, dictate everything" rule of the [[ralph-loop]] and makes [[spec-driven-development]] feel less like writing a document and more like a conversation.

<span style="color:red">**The numbers:** typing runs at ≈60 words per minute, speaking at ≈200 — "your fingers are the bottleneck". Wispr Flow costs ≈$20/month. "The only way to fight an LLM is to speak, not to type."</span>

<span style="color:red">**Which language?** Sessions estimated that prompting in English buys a small precision edge on hard concepts (estimates ranged from ≈1–2% to ≈5–10%, none measured) and fewer thinking tokens. But if you think in your native language, speak it — don't spend brain cycles translating.</span>

## What to dictate

Dictation is especially valuable for [[grill-me|grilling]] requirements and for exploring unfamiliar territory — speaking exploratory thoughts aloud is faster and less inhibited than typing formal prompts.

- <span style="color:red">**Domain storytelling.** Lean back and tell the story with the business person next to you ("once upon a time a pharmacist wanted to return partly expired products… and there was morphine in the return — oh god, *in the return*"). The rambling gives the AI far richer context than a clipped typed prompt.</span>
- <span style="color:red">**Commit messages and PR descriptions** — a 90-second spoken "what and why", condensed by the agent.</span>
- <span style="color:red">**Live UI polishing** — a browser extension that picks a DOM element with the mouse and attaches it to the dictation ("this div is off"), plus an automatic screenshot.</span>
- <span style="color:red">**Brainstorming while walking** — one session cited Karpathy and Simon Willison "writing code walking in the park", and an Anthropic lead who ditched the keyboard for a foot pedal plus dictation, reading responses diagonally.</span>

Dictation handles your self-corrections gracefully — speaking a ticket aloud, the tool silently absorbs the "no, actually…" reversals into clean text. <span style="color:red">Don't bother correcting typos or punctuation in the transcript; the model doesn't care.</span> A complementary input tip for *UI* work: when there's no [[figma-mcp|Figma]] source, **draw a picture instead of describing it** — a hand-drawn wireframe pasted as an image is more token-efficient and less ambiguous than prose (an Anthropic training tip).

## Caveats

- <span style="color:red">**Voice is lossy.** Re-read the synthesized result: in one session a spoken "50" never made it into the spec.</span>
- <span style="color:red">**Whisper-based engines hallucinate on very short clips** — dictate at least two sentences / ≥10 seconds.</span>
- It enables genuinely mobile [[vibe-coding]] — a whole project dictated from a mall using voice plus remote control. The trade-off to watch is the same one [[caveman]] warns about: protect your *attention*, the real last bottleneck, not just your keystrokes.

## Alternatives

<span style="color:red">Native dictation is free on every laptop: macOS System Settings → Dictation (double-tap the Globe key; it works offline, so it is usable even in air-gapped organisations), and Windows [`Win+H`](https://support.microsoft.com/en-us/windows/use-voice-typing-to-talk-instead-of-type-on-your-pc-fec94565-c4bd-329d-e59a-af033fa5689f). Claude Code has a built-in `/voice` command, and the Claude mobile app has a microphone for voice input directly into a chat session.</span> Wispr Flow is the recommended choice for open-plan offices where continuous dictation is needed without disrupting neighbours — <span style="color:red">some teams simply whisper ("ASMR coding")</span>.

<span style="color:red">Victor's own stack: Wispr Flow, his [walkie-talkie](https://github.com/victorrentea/walkie-talkie/) overlay (local Whisper with a LoRA fine-tuned on ≈40 h of his own voice), and a foot pedal to scroll. The training exercise: dictate every prompt for a day, and never copy-paste to or from the agent (see [[chop-ai-waiter]]).</span>

## See also
- [[ralph-loop]]
- [[spec-driven-development]]
- [[grill-me]]
- [[vibe-coding]]
- [[caveman]]
- [[figma-mcp]]
- [[chop-ai-waiter]]
- [[agentic-reconversion]]
- [[2026-06-11-ai-mobile-gaming]]
- [[2026-06-22-ai-sports-betting]]
- [[2026-06-23-ai-device-maker]]
