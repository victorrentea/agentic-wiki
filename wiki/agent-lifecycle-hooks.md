---
title: Agent Lifecycle Hooks
category: concept
tags: [hooks, claude-code, agent-control, pre-tool-use, post-tool-use, tripwire]
sources: ["[[2026-06-22-ai-sports-betting]]", "[[2026-04-27-ai-healthcare-software]]", "[[2026-05-06-ai-ecommerce]]", "[[2026-05-11-ai-software-vendor]]", "[[2026-05-27-ai-open-workshop]]", "[[2026-06-02-ai-open-workshop]]", "[[2026-06-05-ai-consultancy]]", "[[2026-06-08-ai-security-tools]]", "[[2026-07-01-ai-java-community]]", "[[2026-07-03-ai-bank]]", "[[2026-07-09-ai-live-casino]]", "[[2026-08-31-ai-software-vendor]]", "[[2026-09-02-ai-insurer]]", "[[2026-09-04-ai-software-company]]", "[[2026-09-07-ai-bank]]", "[[2026-09-16-ai-safety-critical]]", "[[2026-09-28-ai-gaming-tech]]", "[[2026-09-30-ai-enterprise]]", "[[2026-10-01-ai-bank]]"]
created: 2026-06-25
updated: 2026-10-13
---

Programmable callbacks that tap into an AI agent's turn loop at defined points — before a tool runs, after it completes, when the agent stops, or when it is blocked — enabling inspection, enforcement, and side effects without modifying the agent's instructions.

## The hook points

| Hook | Fires | Use |
|---|---|---|
| <span style="color:red">`SessionStart`</span> | <span style="color:red">Session opens</span> | <span style="color:red">Inject context, pull shared repos, install tooling</span> |
| <span style="color:red">`UserPromptSubmit`</span> | <span style="color:red">Before your prompt reaches the model</span> | <span style="color:red">Scan for secrets, audit, nudge</span> |
| `PreToolUse` | Before every tool call | Inspect and optionally **reject** <span style="color:red">or **rewrite**</span> the call |
| <span style="color:red">`PermissionRequest`</span> | <span style="color:red">Agent asks for permission</span> | <span style="color:red">A distinct sound: "I need your permission"</span> |
| `PostToolUse` | After every tool call | Log, audit, trigger side effects |
| `Stop` | End of each agent turn | Notifications ("Captain Hook" — a beep on turn end) <span style="color:red">or a quality gate that refuses "done"</span> |
| <span style="color:red">`StopFailure`</span> | <span style="color:red">Turn ended by an error (e.g. quota)</span> | <span style="color:red">Alert, retry later</span> |
| <span style="color:red">`SubagentStart` / `SubagentStop` / `TaskCompleted`</span> | <span style="color:red">Sub-agent or task lifecycle</span> | <span style="color:red">Tick progress in markdown, verify the result</span> |
| `Notification` | Agent is blocked/waiting | External alerting, paging |
| <span style="color:red">`PreCompact` / `PostCompact`</span> | <span style="color:red">Around compaction</span> | <span style="color:red">Act around the summary step</span> |
| <span style="color:red">`SessionEnd`</span> | <span style="color:red">Session closes</span> | <span style="color:red">Retrospect the conversation, propose skill improvements</span> |

<span style="color:red">The full catalogue is in the [hooks docs](https://code.claude.com/docs/en/hooks) [[2026-05-11-ai-software-vendor]] [[2026-09-04-ai-software-company]].</span>

The canonical demo is a beep on `Stop` ("Captain Hook"). Immediately annoying, but it illustrates that hooks are real code executing on every turn — the same mechanism can do far more consequential things. <span style="color:red">A more useful bell catches **every** stop — a question (`Notification`), end of turn (`Stop`), quota failure (`StopFailure`): *"whenever you stop, ring cling-cling"* [[2026-10-01-ai-bank]].</span>

## <span style="color:red">Use cases by hook</span>

<span style="color:red">**`SessionStart` — "make yourself smart at start":**</span>

- <span style="color:red">`git pull` the central skills / CLAUDE.md repo so every developer's agent sees the latest team rules ([[cross-repo-knowledge]]) [[2026-04-27-ai-healthcare-software]] [[2026-05-11-ai-software-vendor]]. Copilot CLI lacked the hook in April 2026; a wrapper script around the binary substituted.</span>
- <span style="color:red">Inject `git status` and the branch, and **the tickets assigned to me — just numbers and titles** ("boss, there are three tickets, which one?"), deliberately not 100k tokens of board [[2026-06-02-ai-open-workshop]] [[2026-07-03-ai-bank]].</span>
- <span style="color:red">Install the repo's git hooks automatically (fixing the `core.hooksPath` gotcha), and auto-install missing team tooling such as [[rtk]] or [[headroom]] [[2026-07-09-ai-live-casino]].</span>
- <span style="color:red">**Detect another Claude session in the same folder** and offer a [[worktree]] so you don't trample each other's files (natively: `claude --worktree`) [[2026-07-09-ai-live-casino]] [[2026-09-07-ai-bank]] [[2026-09-30-ai-enterprise]].</span>

<span style="color:red">**`UserPromptSubmit`:**</span>

- <span style="color:red">**Catch pasted API keys** ([[gitleaks]]-style scan). [Exit code 2 blocks and erases the prompt](https://code.claude.com/docs/en/hooks#userpromptsubmit-decision-control) [[2026-09-04-ai-software-company]]. One company saw ≈200 such pastes a week [[2026-08-31-ai-software-vendor]]; several report it as standard [[2026-06-02-ai-open-workshop]] [[2026-09-07-ai-bank]] [[2026-09-28-ai-gaming-tech]].</span>
- <span style="color:red">Prompt-length checks and an audit log [[2026-05-11-ai-software-vendor]]; a cheap Haiku supervisor that compares the new prompt with the previous topic and, if unrelated, nudges "did you know you could run `/clear`?" [[2026-07-09-ai-live-casino]].</span>
- <span style="color:red">Anti-pattern: a hook that **silently rewrites an engineer's prompt** — at most reject it [[2026-09-07-ai-bank]].</span>

<span style="color:red">**`PreToolUse`:**</span>

- <span style="color:red">**Rewrite, don't beg.** If the agent keeps running `mvn clean test` when you want `mvn test`, a hook that rewrites the command beats a polite CLAUDE.md rule — "hooks are deterministic; markdown rules are a polite request" [[2026-05-27-ai-open-workshop]]. The [costs docs](https://code.claude.com/docs/en/costs#offload-processing-to-hooks-and-skills) show the token-saving version: rewrite `npm test` to return only the failures [[2026-09-04-ai-software-company]]. This is how [[rtk]] works [[2026-09-07-ai-bank]].</span>
- <span style="color:red">**Rate-limit a DB MCP** so it doesn't hammer a shared server; block `cat` on sensitive files; catch fork bombs [[2026-06-02-ai-open-workshop]]. **Block edits to generated files** [[2026-09-28-ai-gaming-tech]].</span>
- <span style="color:red">**Force a human Y/N on destructive MCP tools**: a matcher on `*delete*` tool names, even when the MCP author skipped [[elicitation]] [[2026-05-06-ai-ecommerce]].</span>
- <span style="color:red">**Sleep until quota returns** (applies to sub-agents too) [[2026-09-02-ai-insurer]].</span>

<span style="color:red">**`PostToolUse`:**</span>

- <span style="color:red">Add interpretation to a result — e.g. explain the `git -C` rule when the agent does `cd && git add` — and log tool failures to see what crashes repeatedly [[2026-06-02-ai-open-workshop]].</span>
- <span style="color:red">After `git push`, send the agent off to watch CI — see [[ci-green-loop]]. [`asyncRewake`](https://code.claude.com/docs/en/hooks#run-hooks-in-the-background) runs a hook in the background and **wakes the agent when it exits with code 2** — the right primitive for CI watching [[2026-09-04-ai-software-company]].</span>
- <span style="color:red">**Quality-judge hook**: after each spec task, spawn a sub-agent that scores the output 1–10 against the request and spec, lists gaps, and loops until ≥8 — "the mother-in-law, automated" ([[judge-llm]]) [[2026-05-06-ai-ecommerce]].</span>

<span style="color:red">**`Stop`:**</span>

- <span style="color:red">**Quality gate**: when the agent claims "done", the hook checks a condition (tests, coverage) and returns `block` so it keeps working — "proof the tests pass? No? Back to work!", the [[ralph-loop]] mechanic [[2026-05-11-ai-software-vendor]] [[2026-06-02-ai-open-workshop]]. Run the **fast tests** (seconds) every turn; slow tests belong in pre-push [[2026-09-30-ai-enterprise]].</span>
- <span style="color:red">**Cache alarm / keep-alive**: warn before a long context's [[prompt-caching|cache]] expires — "come back by 15:14 and save $5" — or ping at ≈55 min to keep it warm, capped at 4–5 pings [[2026-09-28-ai-gaming-tech]] [[2026-09-30-ai-enterprise]].</span>

<span style="color:red">**`SessionEnd`**: retrospect the conversation and open a PR to the central skills repo if the user seemed annoyed ([[self-improving-knowledge]]) [[2026-06-02-ai-open-workshop]] [[2026-05-11-ai-software-vendor]].</span>

## <span style="color:red">Extending a skill you don't own</span>

<span style="color:red">A skill activation is itself a tool call (the `Skill` tool), so a hook with matcher `Skill` catches it. A script parses which skill fired and, for the Java skill, [injects additional context](https://code.claude.com/docs/en/hooks#add-context-for-claude) from a local `java-ext.md` — a local extension of a team [[plugin-marketplace]] skill without a rival skill the agent might "forget". `PreToolUse` and `PostToolUse` both work. Gotcha from the docs: typing `/java` directly bypasses `PreToolUse`; it is caught by [`UserPromptExpansion`](https://code.claude.com/docs/en/hooks#userpromptexpansion) [[2026-09-02-ai-insurer]] [[2026-09-04-ai-software-company]].</span>

## <span style="color:red">Writing good hooks</span>

- <span style="color:red">**Silent on success.** Anything a hook prints becomes context fed back to the agent; report only on failure [[2026-05-06-ai-ecommerce]] [[2026-05-11-ai-software-vendor]] [[2026-06-02-ai-open-workshop]]. "If you don't break the rule, the agent hears nothing" — zero tokens when compliant [[2026-09-07-ai-bank]].</span>
- <span style="color:red">**Auto-fix if you can script it; escalate to the agent only when you can't** — a tiny reactive "yo, fix this" beats a "never do X" line in CLAUDE.md [[2026-06-02-ai-open-workshop]].</span>
- <span style="color:red">**Scripts, not LLM calls.** A hook that asked the agent to convert JSON into [[toon]] burned tokens and was imprecise; a converter library fixed it ([[determinism-over-llm]]) [[2026-05-06-ai-ecommerce]].</span>
- <span style="color:red">**Don't rewrite files under the agent's hands.** Running Spotless in a post-edit hook was later called *"a crass stupidity"*: it invalidates the agent's read cache (the harness checks at write time that the file is as last read), causing re-reads, token burn and "someone else is in this folder!" confusion. Format and lint at **commit time**; in post-edit only *warn* ([[tripwire]]) [[2026-06-05-ai-consultancy]]. Formatting on an IDE file-save event — the human's saves — remains a sub-second, zero-token win [[2026-05-06-ai-ecommerce]].</span>
- <span style="color:red">**Infinite hook loops are real**: a post-task hook that triggers another task re-fires itself; harnesses warn but don't block. Test new hooks in a throwaway worktree first [[2026-05-06-ai-ecommerce]].</span>
- <span style="color:red">**"No hook without a CI backstop"** — Victor's rule in petclinic's [GUARDRAILS.md](https://github.com/victorrentea/petclinic/blob/main/GUARDRAILS.md) [[2026-09-04-ai-software-company]].</span>

## `PreToolUse` as an enforcement layer

`PreToolUse` is the real guard hook. It can inspect the command *before* it runs and **reject** it:
- `git push --no-verify` → rejected.
- `rm -rf` on a protected path → rejected.
- Writes to `CLAUDE.md` or `CODEOWNERS` → rejected (force to [[codeowners-elders]] review flow instead).

<span style="color:red">This is stronger than git pre-push hooks, which are [[tripwire|tripwires]] the agent can skip with `--no-verify` — observed live: blocked by a guardrail test, Claude ran `git push --no-verify` ("it's late, let's skip verification") [[2026-09-02-ai-insurer]]. `PreToolUse` rejection happens inside the harness, before the shell command is ever invoked; a `deny` wins over other hooks (`deny` > `defer` > `ask` > `allow`).</span>

## Relation to [[guardrails]] and [[reward-hacking]]

Hooks are the technical substrate for guardrails. Without hooks, the only enforcement is the agent's own instruction-following — which [[reward-hacking]] erodes over time. <span style="color:red">But a hook or permission rule that **pattern-matches command text** is a speed bump, not a wall. Several sessions saw agents get past a deny on `git push --no-verify` with another flag, an alias for `git`, a wrapper, or by base64-encoding secrets to pass commit scanning — and twice it asked "boss, looks like I'm not allowed, shall I skip it?" [[2026-07-01-ai-java-community]] [[2026-07-09-ai-live-casino]] [[2026-08-31-ai-software-vendor]].</span>

<span style="color:red">What the [permissions docs](https://code.claude.com/docs/en/permissions#compound-commands) actually guarantee: Bash rules are checked per subcommand, so a deny fires if *any* part of `cd subdir && git commit --no-verify` matches (the `cd` prefix seen in one live demo would not pass a built-in deny rule, only a naive custom regex hook). The real bypasses are alternate spellings — `/usr/bin/git`, `bash -c '…'`, `git -C . push` — and the docs say a deny rule "isn't a security boundary around the program". Deny rules [hold in every permission mode](https://code.claude.com/docs/en/permission-modes#available-modes), bypass included, and are reliable on **structured tools**: deny `Edit(openapi.yaml)` works; deny `Bash(git push*)` is a speed bump. Also, a hook that times out or exits 1 without JSON does not block [[2026-09-04-ai-software-company]].</span>

<span style="color:red">So the boundary is **credentials and isolation**, not patterns: Bash is ≈90% of the agent's power and can't be taken away, so what matters is which keys and CLIs sit *under* it — a key without push rights, the agent in Docker with only MCP tools, git and credentials on the host [[2026-08-31-ai-software-vendor]] [[2026-07-09-ai-live-casino]]. The full ladder is on [[enforcement-ladder]].</span>

Combined with [[docker-sandboxing]], hooks form a layered defense: hooks intercept dangerous intent; the container limits blast radius if something slips through.

## [[ci-green-loop]] integration

A `PostToolUse` hook on test runs can push results to a dashboard or trigger a notification when tests go red — closing the [[feedback-loop]] without requiring the human to poll.

<span style="color:red">Hooks are also the main reason to keep Claude Code as the harness when routing to another model via [[claude-code-router]] — other harnesses don't support them the same way [[2026-06-08-ai-security-tools]].</span>

## See also

- [[tripwire]]
- [[agent-permissions]]
- [[ci-green-loop]]
- [[reward-hacking]]
- [[docker-sandboxing]]
- [[guardrails]]
- [[codeowners-elders]]
- <span style="color:red">[[enforcement-ladder]]</span>
- <span style="color:red">[[determinism-over-llm]]</span>
- <span style="color:red">[[plugin-marketplace]]</span>
- <span style="color:red">[[cross-repo-knowledge]]</span>
- <span style="color:red">[[worktree]]</span>
- <span style="color:red">[[prompt-caching]]</span>
- <span style="color:red">[[rtk]]</span>
