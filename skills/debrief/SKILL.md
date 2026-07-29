---
name: debrief
description: Reviews a session's process, not the work it produced. Use when wrapping up a session, or when asked for a debrief, retrospective, or post-mortem.
---

# Session Retrospective

You are reviewing THIS session (the conversation so far) — not any file, diagram, feature, or
piece of code it produced. The goal is process improvement: how you (the agent) worked, not what
got built. Do not evaluate the quality of any deliverable; that's a different kind of review.

## 0. Load context first

Look back over the actual conversation in this session — the real sequence of tool calls,
questions asked, decisions made, corrections or confirmations the user gave. Don't reconstruct
this from assumption or general impression; look at what actually happened. If a CLAUDE.md,
README, or project memory exists for the current repo, skim it so you know what standing
conventions were already supposed to be followed — but don't assume any of these exist. If the
session touched more than one repo or directory, check each one's own conventions rather than
assuming a single "current repo" applies throughout.

**Scoping note:** match effort to the session. For a very long session, reading the entire
transcript isn't free — default to the most relevant or most recent stretch, and only ask the
user which part they want reviewed if you genuinely can't tell from the request itself. For a
short, uneventful session (a handful of turns, no errors, no corrections), a quick pass
confirming that is enough — don't run every check below at full depth just because the checklist
exists.

**Every finding in every section below must cite a specific, real moment from this session — no
generic advice.** This governs the whole report, not just the sections where it's easy to satisfy.
If part of the session is unavailable because it was compacted or summarized away, don't invent a
plausible-sounding moment to cover that gap — say the coverage is limited for that stretch and
scope findings to what you can actually verify.

## 1. Efficiency and token usage

- Which steps consumed unnecessary tokens — re-reading files that hadn't changed, redundant tool
  calls, guessing at something checkable instead of checking it, more cycles (build/render/test/
  whatever the domain's loop is) than needed, restating information already established earlier
  in the conversation? (If a guess produced a claim that could have been acted on while wrong,
  file it once under section 3's correctness check instead of here.)
- **Errored vs. successful repetition, distinct from the above:** an errored tool call is
  self-flagging — it produces visible failure output you'd naturally scan for. A step that
  succeeded 2+ times but hand-wrote near-identical logic each time (the same parsing script,
  the same multi-part check, the same query shape) leaves no error signal, so it won't surface
  by scanning for failures. The mechanical signal: 3+ tool calls with the same tool name and
  near-identical parameters in the same session — that's the finding, without needing to diff
  every call by hand.
- Was work batched appropriately, or did back-and-forth trickle in one item at a time when it
  could have been gathered first? A mechanical tell: 2+ user messages in a row adding to or
  revising the same request, with nothing in between that explains why the addition couldn't
  have come the first time.
- Two-sided: where should a subagent, fork, or parallel tool call have been used but wasn't —
  AND where was one used well and caught something real? Name both kinds of moment if they
  happened; don't only surface missed opportunities.
- **Overlapping dispatch (skip if fewer than two subagents ran), distinct from the above:** did
  each independently redo work a sibling had already done — the same sources fetched twice, the
  same survey compiled twice — because neither was handed the other's output? A dispatch that
  was correct to make in isolation can still waste a whole context window by duplicating its
  sibling. Read the dispatch prompts against each other —
  they're short and sit close together — rather than diffing everything the agents returned. If
  a sibling's raw output didn't survive context compaction or summarization, say so and skip the
  comparison rather than assuming no duplication occurred.
- **Rework at a context seam (skip if this session was never compacted):** the compaction itself
  is not the finding — long sessions compact, and that is the system working as designed. What
  matters is whether anything had to be re-established afterward: a fact re-fetched, a file
  re-read, a decision re-litigated because the detail
  didn't survive. Look for the rework first; if there is none, there is no finding here, and
  saying nothing is correct. If you can cheaply confirm where the boundary fell (this session's
  own transcript under `~/.claude/projects/` records compaction events), do — but treat that as
  an index for where to look, never as the evidence itself, and never block this check on being
  able to read it.

## 2. Collaboration and communication quality

- Did you ask when you should have (genuine ambiguity, a decision only the user could make) and
  avoid asking when you shouldn't have (something checkable, or already stated)?
- **Timing check, distinct from the above:** did you push for a decision or ask the user to pick
  between options before they'd actually signaled they were done providing input — e.g. asking
  to choose between options while they were still batching feedback and said so? A question can
  be reasonable in content and still be mistimed.
- Did you follow explicit standing instructions and preferences the user had already given
  earlier in the session, or did any get missed or re-violated?
- Was communication with the user appropriately concise, or was there unnecessary narration,
  hedging, or restating of things already said?

## 3. Correctness and process discipline

- Any place a claim was made without verifying it first (asserting something works without
  running it, asserting a fact without checking the source)?
- **Constraint-timing check:** did you verify hard constraints (size limits, format requirements,
  whatever applies in the domain) before finalizing a decision or plan, or only discover them
  after implementing? Front-loading a checkable constraint is cheaper than fixing it after the
  fact.
- Any place a destructive or hard-to-reverse action was taken without confirming first?
- Any place documented project conventions were overlooked?

## 4. What should be captured for next time

- Is there a preference, correction, or confirmed decision from this session that should be
  saved as a memory or written into a project doc (CLAUDE.md/README) so it doesn't need to be
  re-established next time?
- **Regression check:** cross-check each finding above against your existing memory (feedback
  -type entries in particular). A finding that repeats a mistake already corrected once before is
  a more serious regression than a first-time miss — flag it as such, don't let it read the same
  as a novel issue. If no memory system or feedback-type entries are accessible in this
  environment, say so and skip this check rather than assuming none of the findings are
  regressions.
- Is there a missing tool, script, or piece of documentation that would have made this session
  faster? **This is a required check, not an optional one:** count how many times this session
  hand-wrote near-identical logic inline (see the repetition check in section 1). Two or more
  occurrences is itself the signal that a reusable script or skill asset is missing — independent
  of whether any individual attempt errored.

## Output format

Structure the report as four headed sections, in this order. Sections 1–3 above feed Concrete
issues; section 4 feeds Suggested improvements; What went well draws from anything positive
noticed while working through sections 1–3.

### Overall assessment
One or two sentences, direct. If section 0's scoping note applied — you reviewed only part of
the session rather than all of it — say so here (e.g. "reviewing only the last N exchanges") so
the reader knows what this report does and doesn't cover.

### Concrete issues
List most severe first. Each grounded in a specific real moment from this session (not a
hypothetical), prefixed with both a severity marker and label so a real process failure doesn't
read the same as a minor nitpick: `🔴 High:` for wasted work, wasted tokens, or a claim that
risked being acted on while wrong; `🟡 Medium:` for a noticeable inefficiency or collaboration
slip that cost real time; `🟢 Low:` for polish worth naming once and not repeating. A finding
that matches more than one criterion (e.g. both an unverified claim and a repeat of a documented
regression) escalates to the higher of the two tiers, not the first one identified. State the
observable behavior and its impact, not intent or blame (e.g. "used X, causing Y" rather than
"violated the rule by doing X"). When a finding rests on multiple pieces of evidence, list them
as short sub-bullets instead of one long sentence. Calibrate rather than defaulting to the middle
tier: most clean sessions should have zero or one 🔴, reserved for genuine wasted work or a wrong
claim that could have shipped — don't inflate routine inefficiency upward, and don't downgrade a
real regression to look safe.

### What went well
What's worth repeating, stated briefly — what happened and why it mattered, not a narrative.

### 💡 Suggested improvements
A single flat list. Every item here requires the user's approval before anything is applied —
this skill produces a report, not automatic changes (see Rules below). Name the specific
destination for each item — don't just say "edit CLAUDE.md". See
[references/suggested-improvements.md](references/suggested-improvements.md) for the full set of
destinations and how to choose between them.

## Rules

- Don't manufacture criticism to fill out every section; if a section has nothing real, say so
  briefly and move on.
- This skill produces a report, not automatic changes. Stop after presenting it — only save a
  memory or edit a doc if the user approves, unless they've said in advance this invocation
  should auto-apply safe changes.
