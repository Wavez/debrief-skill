# Suggested improvements: destinations

Read this once you have at least one suggested improvement to route — it defines the possible
destinations and how to choose between them. Name the specific destination for each item — don't
just say "edit CLAUDE.md":

- **Project/local memory** — personal to this repo, not committed.
- **Repo `CLAUDE.md`** — committed, shared with anyone who opens this repo.
- **Global `CLAUDE.md`** (`~/.claude/CLAUDE.md`) — applies across every repo and session, not
  just this one. Flag explicitly that it's global so the user knows the wider blast radius
  before agreeing.
- Extracting hand-written logic into a reusable script or skill asset.
- **A hook** (`settings.json`) — for a standing instruction that's already been through
  memory/CLAUDE.md once and still regressed. Memory can only describe desired behavior; a hook
  can enforce it. Don't default to this for a first-time miss — text guidance is cheaper and
  usually sufficient. Reach for it specifically when the regression check (section 4) shows the
  same rule failing again despite already being documented. Scope the hook to match the
  regressed rule's own scope, not to whichever repo you happen to be in right now: a habit that
  came from *global* `CLAUDE.md` needs a *global* hook (`~/.claude/settings.json`) — a
  project-only hook would leave every other repo's sessions still exposed to the same
  regression. A convention that came from *repo* `CLAUDE.md` or project memory needs a
  *project* hook instead (`.claude/settings.json`, or `.claude/settings.local.json` if it
  shouldn't be shared with other contributors).

These destinations aren't mutually exclusive — a finding can belong in more than one. Ask
independently for each, don't stop at the first match:
- True regardless of which repo you're in (a general working-style preference)? → global
  `CLAUDE.md`.
- Worth a personal, detailed record for next time (the incident, the "why," the full context)?
  → project memory.
- A reusable fact, technique, or convention that anyone working in this codebase — including a
  fresh session with no memory of this one — would need to redo the same work? → repo
  `CLAUDE.md`, even if it's *also* going to project memory in more detail.
- A rule that's already been documented (memory or CLAUDE.md) and regressed anyway? → a hook,
  in addition to keeping the existing documentation as context for *why* the hook exists — scope
  it to match where that documentation lives (global CLAUDE.md → global hook, repo CLAUDE.md/
  project memory → project hook), not to the current repo by default.
