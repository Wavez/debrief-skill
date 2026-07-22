---
name: debrief
description: Use when wrapping up a Claude Code session, or when asked for a debrief, session retrospective, or post-mortem. Reviews how the session went, not what it built — catches mistimed questions, repeated mistakes, and wasted tool calls that a code review can't see.
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
conventions were already supposed to be followed — but don't assume any of these exist.

**Scoping note:** for a very long session, reading the entire transcript isn't free. Focus on the
most relevant or most recent stretch, or ask the user which part they want reviewed, rather than
retro-ing the whole thing by default.

## 1. Efficiency and token usage

- Which steps consumed unnecessary tokens — re-reading files that hadn't changed, redundant tool
  calls, guessing at something checkable instead of checking it, more cycles (build/render/test/
  whatever the domain's loop is) than needed, restating information already established earlier
  in the conversation?
- **Errored vs. successful repetition, distinct from the above:** an errored tool call is
  self-flagging — it produces visible failure output you'd naturally scan for. A step that
  succeeded 2+ times but hand-wrote near-identical logic each time (the same parsing script,
  the same multi-part check, the same query shape) leaves no error signal, so it won't surface
  by scanning for failures. Find it by comparing tool-call bodies against earlier calls in the
  same session, not by looking for what broke.
- Was work batched appropriately, or did back-and-forth trickle in one item at a time when it
  could have been gathered first?
- Two-sided: where should a subagent, fork, or parallel tool call have been used but wasn't -
  AND where was one used well and caught something real? Name both kinds of moment if they
  happened; don't only surface missed opportunities.

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
  as a novel issue.
- Is there a missing tool, script, or piece of documentation that would have made this session
  faster? **This is a required check, not an optional one:** count how many times this session
  hand-wrote near-identical logic inline (see the repetition check in section 1). Two or more
  occurrences is itself the signal that a reusable script or skill asset is missing — independent
  of whether any individual attempt errored.

## Output format

Structure the report as four headed sections, in this order:

### Overall assessment
One or two sentences, direct.

### Concrete issues
Each grounded in a specific real moment from this session (not a hypothetical), prefixed with
both a severity marker and label so a real process failure doesn't read the same as a minor
nitpick: `🔴 High:`, `🟡 Medium:`, `🟢 Low:`. State the observable behavior and its impact, not
intent or blame (e.g. "used X, causing Y" rather than "violated the rule by doing X"). When a
finding rests on multiple pieces of evidence, list them as short sub-bullets instead of one long
sentence.

### What went well
What's worth repeating, stated briefly — what happened and why it mattered, not a narrative.

### Suggested improvements
Split into two prefixed lists: 🤖 safe to apply automatically (e.g. a memory save) vs. 👤
requiring the user's approval (e.g. editing CLAUDE.md, or extracting logic that was hand-written
2+ times this session into a reusable script or asset in the relevant skill).

## Rules

- Every finding must cite a specific, real moment from this session — no generic advice.
- Don't manufacture criticism to fill out every section; if a section has nothing real, say so
  briefly and move on.
- This skill produces a report, not automatic changes. Stop after presenting it — only save a
  memory or edit a doc if the user approves, unless they've said in advance this invocation
  should auto-apply safe changes.
