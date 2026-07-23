<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/logo-light.svg">
  <img src="assets/logo-light.svg" alt="debrief" height="56">
</picture>

[![Claude Code](https://badgen.net/badge/Claude%20Code/Skill/blue?icon=claude)](https://claude.ai/code)
[![Version](https://badgen.net/github/release/Wavez/debrief-skill)](https://github.com/Wavez/debrief-skill/releases)
[![License: MIT](https://badgen.net/github/license/Wavez/debrief-skill?color=green)](https://github.com/Wavez/debrief-skill/blob/main/LICENSE)

**Reviews how the session went, not just what it produced.** A single-file Claude Code skill for
session retrospectives, with no scripts or dependencies - it catches mistimed questions,
repeated mistakes, and the wasted tool calls and tokens that a code review can't see.

*Origin story (soon): [I hit a usage limit and found the real bottleneck wasn't
tokens](PLACEHOLDER_MEDIUM_URL).*

## Install

```
/plugin marketplace add Wavez/debrief-skill
/plugin install debrief-skill@debrief-skill
```

## Auto updating

Run `/plugin` → **Marketplaces** → select `debrief-skill` → **Enable auto-update**.

## Usage

Run `/debrief` at the end of a session so Claude stops repeating the same process mistakes and
gets better at working with you over time.

Read-only and self-contained: it never edits files or saves memory unless you explicitly
approve it.

## What it checks

- **Efficiency and token usage** — redundant tool calls, unnecessary re-reads, and missed
  batching or parallelization, so you catch wasted tokens and time before they repeat next
  session.
- **Collaboration and communication quality** — including a **timing check**: flags a question
  that's reasonable in content but asked before the user signaled they were done giving feedback.
- **Correctness and process discipline** — unverified claims, unconfirmed hard-to-reverse actions,
  missed or re-violated standing instructions, and a **constraint-timing check**: whether hard
  requirements were confirmed before finalizing a plan, or only discovered after the work was
  already done.
- **Follow-through for next time** — including a **regression check**: cross-references findings
  against saved feedback/memory, so a repeated mistake reads as a regression, not a first-time
  miss.

## Example output

```
### Overall assessment
Mostly efficient session, one timing slip worth flagging.

### Concrete issues

🔴 **High: Claimed the fix was "done" before running the test suite.**

🟡 **Medium: Asked whether the architecture was finalized while you were still listing
constraints.**
- A mistimed question — reasonable in content, asked before you'd signaled you were done
  giving feedback.

🟢 **Low: Re-read config.py three times after no edits had been made to it.**

### What went well
- Batched the four independent file edits into a single parallel tool call.

### Suggested improvements

🤖 Safe to apply automatically:
- None needed this session.

👤 Requiring your approval:
- Save a memory noting the timing preference above.
```

## When to use it

- **Use** when wrapping up a Claude Code session, or when asked for a debrief, a session
  retrospective, or a post-mortem.
- **Not for** reviewing the code or artifacts a session produced - other tools handle that.

## Why not an existing retrospective skill?

| Alternative | What it does differently |
|---|---|
| [`bitwarden/ai-plugins` `claude-retrospective`](https://github.com/bitwarden/ai-plugins/tree/main/plugins/claude-retrospective) | Heavier: pulls git history and examines existing code-quality metrics via companion shell scripts from Bitwarden's plugin ecosystem. A full session audit, not a lightweight process check. |
| [`TerenceBristol/claude-improve`](https://github.com/TerenceBristol/claude-improve) | A continuous, multi-run configuration-improvement system tracking acceptance rates across sessions over time — an ongoing machine, not a single end-of-session retrospective. |
| [`session-report`](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/session-report) (official Anthropic plugin) | Narrative call-outs on token waste and cost anomalies from usage data — not a judgment of the collaboration or process itself. |

## Changelog

See [CHANGELOG.md](docs/CHANGELOG.md) for release history.

## License

[MIT](LICENSE)
