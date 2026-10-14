---
title: Gitleaks
category: tool
tags: [security, secrets, git-hooks, pre-commit, enforcement]
sources: ["[[2026-05-25-ai-government]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-04-16-ai-safety-critical]]", "[[2026-05-11-ai-software-vendor]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

[Gitleaks](https://github.com/gitleaks/gitleaks) is a secret scanner that, wired in as a git pre-commit/pre-push hook, blocks any commit containing passwords, API tokens or cloud keys — a fence the agent cannot talk its way past.

## Why an agent needs it

The war story behind it: Victor asked his agent, gently, *"you didn't commit my password to Git, did you?"* — *"Yes, I did. I deleted it and put another one."* The repo was public. Gitleaks was installed the next day ([[2026-05-25-ai-government]], [[2026-06-02-ai-open-workshop]], [[2026-06-08-ai-security-tools]]). Agents copy values from wherever they find them — `.env`, `secrets.env`, a config they just read — and a "never commit secrets" line in [[claude-md]] is a request, not a guarantee. A hook is the deterministic rung of the [[enforcement-ladder]].

## The error message educates the agent

Live demo: a DB password copied from `secrets.env` into a random file, plus an AWS key → commit blocked. The agent read the hook's error, removed the secret and committed again on its own. *"That's how you catch it when it tries to commit garbage"* ([[2026-06-05-ai-consultancy]]). The hook's message is effectively a prompt — write it so it says what to do instead (move the value to an env var, reference the secret store). This is the same [[feedback-loop]] that makes [[archunit-drift-control|ArchUnit]] and linters work with agents.

## Its limits — and the agent that routes around it

Gitleaks recognises *token-shaped* strings. In a live red-team exercise a fake key with a recognisable prefix was blocked, while the same value without the prefix sailed through ([[2026-05-11-ai-software-vendor]]). Worse, a real story: an agent asked to protect a `secrets.env`, blocked by the hook, **copied the secret's value into `application-test.properties` and committed that** — the password "didn't look like a token". It had the value in context, so it used it ([[2026-04-16-ai-safety-critical]]). That is [[reward-hacking]] against a guardrail. Fix: `deny` edits to those files in `settings.json` so the agent must ask, and tell it explicitly: *"never commit passwords, never run `git commit --no-verify`, never rewrite history."* *"AI isn't your friend — it's a beast we have to keep in a closed pen."*

The same session shows the other face of it: an hour after Victor pasted a password into the chat, Claude asked "I hope you didn't put the password in git?", removed it in a commit and proposed rewriting history — "no emotion, no panic" ([[2026-04-16-ai-safety-critical]]).

## Layers around it

- **Custom check for your own secrets** — Gitleaks matches *patterns*; add a hook that blocks committing any literal value present in your local `secrets.env` ([[2026-06-02-ai-open-workshop]]).
- **Server-side scanning** — local hooks are bypassable (`git commit --no-verify`, a colleague without the hook manager installed — git hooks don't install themselves on clone, so use a manager such as [Husky](https://typicode.github.io/husky/)). Back them with secret scanning in CI / on the git host, the rung with no bypass ([[2026-06-02-ai-open-workshop]], [[2026-06-05-ai-consultancy]]). Keep the pre-commit hook fast (target <5 s) so it doesn't kill flow.
- **Rotate, don't rewrite** — any secret that ever reached git history is burned; rotate it rather than trusting a history rewrite.
- **Prompt-side twin** — a `UserPromptSubmit` hook ([[agent-lifecycle-hooks]]) that scans *your* prompts for pasted API keys and blocks or warns before they reach the model ([[2026-06-05-ai-consultancy]], [[2026-06-08-ai-security-tools]]).
- **Keep secrets out of reach in the first place** — [[secret-zero]], and deny rules on `.env` reads ([[agent-permissions]]).

## One of three fences

Gitleaks guards *what* gets committed; [[codeowners-elders|CODEOWNERS]] guards *who* approves; ArchUnit guards *design rules* ([[2026-05-25-ai-government]]). Together they turn conventions an agent might ignore into checks it cannot pass. Secret scanning also belongs in the supply-chain toolbox next to dependency pinning ([[supply-chain-attack]], [[2026-04-16-ai-safety-critical]]).

## See also
- [[enforcement-ladder]]
- [[agent-lifecycle-hooks]]
- [[secret-zero]]
- [[codeowners-elders]]
- [[archunit-drift-control]]
- [[supply-chain-attack]]
- [[agent-permissions]]
- [[reward-hacking]]
