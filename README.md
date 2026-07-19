# debrief

[![Claude Code](https://img.shields.io/badge/Claude_Code-compatible-D97757?logo=claude&logoColor=white)](https://claude.ai/code)
[![Version](https://img.shields.io/github/v/release/Wavez/debrief-skill)](https://github.com/Wavez/debrief-skill/releases)
[![License: MIT](https://img.shields.io/github/license/Wavez/debrief-skill)](https://github.com/Wavez/debrief-skill/blob/main/LICENSE)

**Reviews how the session went, not what it built.** A single-file Claude Code skill for
session retrospectives, with no scripts or dependencies — it catches mistimed questions,
repeated mistakes, and the wasted tool calls and tokens that a code review can't see.

## Install

```
/plugin install debrief-skill@Wavez/debrief-skill
```

Or copy it in manually:

```bash
cp -r skills/debrief ~/.claude/skills/debrief
```

Run it at the end of a session so Claude stops repeating the same process mistakes and gets
better at working with you over time.

Read-only and self-contained: it never edits files or saves memory unless you explicitly
approve it, and never pulls in anything beyond the current conversation.

## What it checks

- **Efficiency and token usage** — redundant tool calls, unnecessary re-reads, and missed
  batching or parallelization, so wasted tokens and time get flagged instead of repeated next time.
- **Collaboration and communication quality** — including a **timing check**: flags a question
  that's reasonable in content but asked before the user signaled they were done giving feedback.
- **Correctness and process discipline** — unverified claims, unconfirmed hard-to-reverse actions,
  and missed or re-violated standing instructions, so a mistake that matters doesn't slip through
  unflagged.
- **What should be captured for next time** — including a **regression check**: cross-references
  findings against saved feedback/memory, so a repeated mistake reads as a regression, not a
  first-time miss.

## Example output

```
## Debrief

**Overall assessment:** Mostly efficient session, one timing slip worth flagging.

**Concrete issues**
- **Collaboration, minor — Mistimed question**: Asked whether the architecture was finalized
  while you were still listing constraints.
- **Efficiency, moderate — Redundant re-read**: Re-read config.py three times after no edits
  had been made to it.
- **Process, critical — Unverified claim**: Claimed the fix was "done" before running the
  test suite.

**What went well**
- Batched the four independent file edits into a single parallel tool call.

**Suggested improvements**
- Safe to apply automatically: none needed this session.
- Needs your OK: save a memory noting the timing preference above.
```

## When to use it

- **Use** when wrapping up a Claude Code session, or when asked for a debrief, a session
  retrospective, or a post-mortem.
- **Not for** reviewing the code or artifacts a session produced — other tools handle that.

## Why this and not an existing retrospective skill

| Alternative | What it does differently |
|---|---|
| [`bitwarden/ai-plugins` `claude-retrospective`](https://github.com/bitwarden/ai-plugins/tree/main/plugins/claude-retrospective) | Heavier: pulls git history and runs code-quality and security scans, with companion scripts from Bitwarden's plugin ecosystem. A full session audit, not a lightweight process check. |
| [`accidentalrebel/claude-skill-session-retrospective`](https://github.com/accidentalrebel/claude-skill-session-retrospective) | Narrative: produces a "lessons learned, blog-post-ready" writeup, not a structured critique of the collaboration. |
| [`TerenceBristol/claude-improve`](https://github.com/TerenceBristol/claude-improve) | A continuous, multi-run configuration-improvement system tracking acceptance rates across sessions over time — an ongoing machine, not a single end-of-session retrospective. |
| [`session-report`](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/session-report) (official Anthropic plugin) | Pure token/cost/cache analytics — no qualitative judgment about how the session went. |

## License

MIT
