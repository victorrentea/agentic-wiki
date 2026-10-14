---
title: Approval Fatigue
category: security
tags: [security, permissions, human-factors, auto-mode, claude-code, sandbox]
sources: ["[[2026-09-07-ai-bank]]", "[[2026-06-08-ai-security-tools]]", "[[2026-08-13-ai-insurer]]", "[[2026-09-14-ai-industrial]]", "[[2026-09-24-ai-bank]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-30-ai-safety-critical]]", "[[2026-04-07-ai-product-team]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-13-ai-it-services]]", "[[2026-05-18-secure-coding-payments]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-25-ai-bank-migration]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-10-01-ai-bank]]"]
created: 2026-10-13
updated: 2026-10-13
new_in: 2026-10-13
---
<span style="color:red">🆕 New in the 2026-10-13 merge</span>

Approval fatigue is the measurable decay of human attention to permission prompts: after a handful of approvals people approve almost anything, so per-call manual approval is not a real safety control.

## The evidence

- **Anthropic's own numbers.** Claude Code users approve **93%** of permission prompts (Anthropic engineering, [March 2026](https://www.anthropic.com/engineering/claude-code-auto-mode)); the newer figure is **97%**, and in the same analysis human review caught just **13.6%** of dangerous commands versus **89%** for auto mode ([Claude blog, Aug 2026](https://claude.com/blog/auto-mode-default-in-claude-code)). That post announced auto mode as the default for Pro/Max/Team plans from 14 Aug 2026.
- **Neuroscience.** A BYU fMRI study found *"a dramatic drop in the visual processing centers of the brain after only the second exposure to a warning, with further decreases with subsequent exposures"* ([BYU](https://scholarsarchive.byu.edu/facpub/9306/)).
- **Clinical alert fatigue**, the older cousin: *"excessive and/or irrelevant alerts lead recipients to ignore, overlook, or override alerts"* ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC13385993/)). See also [WorkOS: approval fatigue in agent governance](https://workos.com/blog/approval-fatigue-agent-governance).
- **Autonomy grows with experience.** Anthropic's [measuring agent autonomy](https://www.anthropic.com/research/measuring-agent-autonomy) reports ≈20% of new users' sessions (<50 sessions) on full auto-approve, rising past 40% at ≈750 sessions — which Anthropic reads not as fatigue but as a shift from approving each action to supervising and intervening.

Rules of thumb from the training rooms — **not study results**: "after ≈10 approvals you're fatigued and will approve a [fork bomb](https://en.wikipedia.org/wiki/Fork_bomb)" ([[2026-09-07-ai-bank]], [[2026-09-14-ai-industrial]], [[2026-08-31-ai-software-vendor]]); "wrong approvals double every ten approvals" or "grow exponentially after the first ≈10" ([[2026-09-28-ai-gaming-tech]], [[2026-10-01-ai-bank]] — the latter explicitly quoted from memory); "by approval number 50 detection has collapsed" ([[2026-08-13-ai-insurer]]); "within 2 to 30 days, 95% of developers grant the agent all permissions" ([[2026-04-07-ai-product-team]]). No source gives a threshold; the sourced numbers are the ones above. What the rooms agree on is the trajectory: two weeks of yes/no is what drives people to YOLO ([[2026-05-13-ai-it-services]], [[2026-05-18-secure-coding-payments]]) — approving commands one by one is **theatre**, even for bash experts ([[2026-09-25-ai-bank-migration]]).

## Why reading the prompt doesn't work

- **Nobody can parse a pipe at a glance.** *"It's arrogance to approve tools manually"* — a `sed | awk | grep` chain, or a three-line compound command, gets an Enter, not a review.
- **The dangerous part hides in the middle.** One demo slipped `cd subdir && git commit --no-verify` past a human ([[2026-07-01-ai-java-community]]); another hid a `git push --force` at line 55 of a long compound command ([[2026-07-30-ai-safety-critical]]). Claude crams several actions per line to save tokens, which defeats human review further. Claude Code *does* check deny/ask rules against every subcommand of a compound ([permissions docs](https://code.claude.com/docs/en/permissions#compound-commands)) — the weak link is the human eye, not the rule matcher.
- **Bash is a language.** Aliases, `bash -c`, a script written to `/tmp`, an absolute path (`/usr/bin/git`), `git -C .`, a package post-install — a deny rule matches command *text*, so it is a [[tripwire]], *"a friendly slap on the back of the head, not security"* ([[2026-06-08-ai-security-tools]]). The docs say the same: a deny rule *"isn't a security boundary around the program"*.

## Remedies

1. **Never "Allow all"** for a session — the *"remove the French language: `rm -fr /`"* joke is how it ends ([[2026-09-24-ai-bank]]).
2. **Let a classifier judge instead of you.** Auto mode / an autopilot — a second, clean LLM checking each tool call against the user's intent ([[judge-llm]], [[agent-permissions]]) — catches far more than a tired human, though it too can approve something dangerous.
3. **Deny rules for the known-bad** — deny beats allow, and catches the common spelling.
4. **Restrict the keys behind the tools.** The token's scope is the blast radius ([[production-safety]], [[draft-only-email]]).
5. **Move the safety into tests, CI, CODEOWNERS and the sandbox** so approvals stop mattering ([[docker-sandboxing]], [[os-sandbox]]). *"Approval fatigue doesn't justify suicide"*: `--dangerously-skip-permissions` at the office is forbidden **outside** a sandbox.
6. **Raise attention for the critical minutes.** Keep auto mode on day to day, but switch it off for the ≈10 minutes of a destructive operation (a migration, a prod data fix) and read the scripts yourself ([[2026-08-13-ai-insurer]]).

## See also
- [[agent-permissions]]
- [[docker-sandboxing]]
- [[os-sandbox]]
- [[judge-llm]]
- [[cognitive-debt]]
- [[system-2-fatigue]]
- [[tripwire]]
- [[production-safety]]
