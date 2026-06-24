---
title: Hardware Mock
category: pattern
tags: [testing, mobile, bluetooth, protobuf, ci, hardware]
sources: ["[[2026-06-23-ai-garmin]]"]
created: 2026-06-24
updated: 2026-06-24
new_in: 2026-06-24
---

<span style="color:red">🆕 New in Garmin workshop Day 2 (2026-06-24)</span>

A hardware mock swaps a physical device (Bluetooth peripheral, sensor, CAN bus dongle) for a software emulator that speaks the same application-layer protocol over a loopback transport, making CI-testable builds possible without the physical hardware.

## The pattern

Physical devices are inherently non-deterministic and unavailable on CI. The fix: swap the hardware-communication module at build time:
- **Production build** — the real Bluetooth/ANT+/serial driver.
- **CI / test build** — a software stub that speaks the same application-layer protocol (e.g. protobuf) over a loopback socket or named pipe.

This makes the app agnostic to the transport: it talks protobuf-over-loopback in tests and protobuf-over-Bluetooth in production, with zero code change outside the shim.

## Why it matters for agentic workflows

An agent tasked with fixing a bug in the Bluetooth integration cannot do so if the only real device is "lost in a cupboard." Without a hardware mock, the agent's feedback loop is broken — it can write code but can't verify it. With the stub wired into the project's `AGENTS.md` build instructions, the agent runs its own integration tests on every attempt, giving it the same [[feedback-loop]] a web developer has with a browser. This is the mobile/embedded equivalent of test-double injection.

## Generalization

The pattern applies to any hardware protocol: BLE, ANT+, CAN bus, serial UART, proprietary RF. The shim's job is only protocol translation — the application layer sees only the message types it already knows.

## See also
- [[feedback-loop]]
- [[acceptance-test-bdd]]
- [[adb-automation]]
- [[ralph-loop]]
- [[agents-md-discipline]]
- [[2026-06-23-ai-garmin]]
