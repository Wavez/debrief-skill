# CLAUDE.md

Instructions for Claude Code sessions working in this repository.

## Commit message format: Conventional Commits (required)

Every commit in this repo MUST follow [Conventional Commits](https://www.conventionalcommits.org/):

    <type>[optional scope]: <description>

    [optional body]

    [optional footer(s)]

Common types used here: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `ci`.

- A breaking change to the skill's behavior or interface: use `feat!:` (or `fix!:`), or add a
  `BREAKING CHANGE: <description>` footer.
- Keep the description short, imperative, lowercase after the colon (e.g. `feat: add scope option`).
- `skills/debrief/SKILL.md` is the skill itself, not documentation — this is a single-file skill
  with no separate source code, so edits to it change actual runtime behavior (the report format,
  the checks performed) and should be typed `feat`/`fix`/`refactor` accordingly, based on what
  changed. Reserve `docs:` for README/example-only edits that don't touch `SKILL.md`.

### Why this matters here

This repo's releases are fully automated by [release-please](https://github.com/googleapis/release-please)
(`.github/workflows/release-please.yml`). It reads commit messages on `main` to decide the next
semver version automatically:

- `fix:` -> patch bump
- `feat:` -> minor bump
- `feat!:` / `BREAKING CHANGE:` footer -> major bump
- `docs:`, `chore:`, `refactor:`, `test:`, `ci:` -> no version bump, but still appear in the changelog

release-please keeps a standing "Release PR" up to date as conventional commits land on `main`. That
PR bumps `.github/version.txt`, `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, and
`docs/CHANGELOG.md` together. Merging that PR is what actually cuts the release: it creates the git
tag and the GitHub Release. It is not auto-merged — merge it deliberately when ready to ship.

A non-conforming commit message on `main` won't be rejected (there's no branch protection or PR
gate in this repo), but it will show up as a failed check from `.github/workflows/commitlint.yml`,
and it may cause release-please to miscategorize or drop the change from the changelog. So: always
format commit messages as Conventional Commits, even for small or reflexive commits.

## Validating changes to the release machinery

Before pushing any change to `.github/release-please-config.json`,
`.github/.release-please-manifest.json`, `.github/workflows/release-please.yml`, or
`.github/commitlint.yml`, dry-run it locally against a throwaway branch first. This is not a CI
job — it's deliberately a manual step, since the release machinery only changes rarely and a
permanent workflow to test the other workflows isn't worth the added CI surface for a one-file
skill. `--dry-run` makes zero writes to GitHub (no branch, no PR, no commit created by the tool
itself); pointing it at a throwaway branch means `main` is never touched either:

    git checkout -b test/validate-release-config
    git commit --allow-empty -m "feat: test"
    git push -u origin test/validate-release-config

    npx --yes release-please release-pr \
      --token="$(gh auth token)" \
      --repo-url=Wavez/debrief-skill \
      --config-file=.github/release-please-config.json \
      --manifest-file=.github/.release-please-manifest.json \
      --target-branch=test/validate-release-config \
      --dry-run

Confirm the output shows the expected version bump and lists the correct files under `updates:`
(currently `docs/CHANGELOG.md`, `.github/version.txt`, `.claude-plugin/plugin.json`,
`.claude-plugin/marketplace.json`, `.github/.release-please-manifest.json`). Then clean up:

    git checkout main
    git branch -D test/validate-release-config
    git push origin --delete test/validate-release-config

## Debugging the Version and License badges

The README's Version and License badges (`badgen.net/github/release/...` and
`badgen.net/github/license/...`) are proxied through GitHub's camo cache when rendered on
github.com, which caches independently of badgen.net's own cache and is unreliable to `PURGE`
directly (confirmed: a `PURGE` request returns `200 {"status":"ok"}` without reliably
invalidating the cached image). Worse, a badgen.net request that itself fails (e.g. hitting
GitHub's unauthenticated API rate limit) gets cached as an error image the same way a normal
response would — camo has no concept of "this was a failure, don't cache it."
`.github/workflows/release-please.yml` works around this by appending `?ref=<release-sha>` to
both badge URLs after every release, forcing a genuinely new URL each time instead of depending
on cache invalidation.

To check what version a badge (or its camo-proxied copy) is actually rendering:

    curl -s "$URL" | grep -oE '<text[^>]*>v?[0-9]+\.[0-9]+\.[0-9]+</text>' | tail -2

Find the live camo URL for a specific badge by fetching the repo's GitHub page and grepping for
`camo\.githubusercontent\.com` — the original badge URL is hex-encoded in the second path segment.

## Other conventions

- `.claude-plugin/plugin.json`'s `version` and `.claude-plugin/marketplace.json`'s
  `plugins[0].version` must always match — they're kept in sync automatically by release-please's
  `extra-files` config, so don't hand-edit either version field.
- Don't manually create git tags or GitHub Releases; that's release-please's job once its PR is merged.
