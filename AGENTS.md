# Repository guide for AI agents

General cross-repo conventions (commits, branching, PRs, linter tuning,
security scanning) live in the global `~/.config/opencode/AGENTS.md` and are
already loaded. This file only records what is specific to THIS repo.

## What this repo is

- Purpose: define a **custom MegaLinter Docker flavor** bundling the linters
  used across the maintainer's repositories.
- Current state: this is a **config- and CI-only scaffold**. There is no
  application source, no build, no test suite, and (as of now) **no Dockerfile
  / flavor definition or `flavors/*` manifest yet**. Do not invent build/test
  commands - there are none to run locally.
- The single source of truth for linter selection and tuning is
  `.mega-linter.yml`; sibling tool configs are `.rumdl.toml`, `lychee.toml`,
  `.checkov.yml`. Edit those, don't restate their contents elsewhere.

## CI quirks (verified in `.github/workflows/`)

- `mega-linter.yml` runs MegaLinter using the **upstream `documentation`
  flavor** (`oxsecurity/megalinter/flavors/documentation`), NOT the custom
  flavor this repo intends to produce. Keep this in mind - the flavor being
  built and the flavor running CI are currently different things.
- The mega-linter job only runs on **branches other than `main`**, is skipped
  for `chore/renovate/*` and `release-please--*` branches, and only lints
  changed `**/*.md` files (plus extracts bash from markdown code blocks for
  validation). Pushing markdown to a feature branch is what triggers linting.
- `FAIL_IF_MISSING_LINTER_IN_FLAVOR: true` - a linter enabled in
  `.mega-linter.yml` but absent from the running flavor fails the build.
- Releases: `release-please.yml` runs on `main` with `release-type: simple`
  and bumps the version from conventional commits. Do not hand-edit versions or
  `CHANGELOG.md` (it is generated and excluded from all linters/link checks).

## Notes

- Sole code owner / reviewer: `@ruzickap` (`.github/CODEOWNERS`).
