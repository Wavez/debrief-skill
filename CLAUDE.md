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

### Why this matters here

This repo's releases are fully automated by [release-please](https://github.com/googleapis/release-please)
(`.github/workflows/release-please.yml`). It reads commit messages on `main` to decide the next
semver version automatically:

- `fix:` -> patch bump
- `feat:` -> minor bump
- `feat!:` / `BREAKING CHANGE:` footer -> major bump
- `docs:`, `chore:`, `refactor:`, `test:`, `ci:` -> no version bump, but still appear in the changelog

release-please keeps a standing "Release PR" up to date as conventional commits land on `main`. That
PR bumps `version.txt`, `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, and
`CHANGELOG.md` together. Merging that PR is what actually cuts the release: it creates the git tag
and the GitHub Release. It is not auto-merged — merge it deliberately when ready to ship.

A non-conforming commit message on `main` won't be rejected (there's no branch protection or PR
gate in this repo), but it will show up as a failed check from `.github/workflows/commitlint.yml`,
and it may cause release-please to miscategorize or drop the change from the changelog. So: always
format commit messages as Conventional Commits, even for small or reflexive commits.

## Other conventions

- `.claude-plugin/plugin.json`'s `version` and `.claude-plugin/marketplace.json`'s
  `plugins[0].version` must always match — they're kept in sync automatically by release-please's
  `extra-files` config, so don't hand-edit either version field.
- Don't manually create git tags or GitHub Releases; that's release-please's job once its PR is merged.
