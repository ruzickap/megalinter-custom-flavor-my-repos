# Repository guide for AI agents

General cross-repo conventions (commits, branching, PRs, linter tuning,
security scanning) live in the global `~/.config/opencode/AGENTS.md` and are
already loaded. This file only records what is specific to THIS repo.

## What this repo is

- Purpose: define and publish a **custom MegaLinter Docker flavor** bundling
  only the linters used across the maintainer's repositories, to keep the image
  small.
- There is **no application source, build, or test suite** to run locally. The
  repo is config + CI + a consumable GitHub Action (`action.yml`). Do not invent
  build/test commands.
- The published artifacts are a Docker image on ghcr.io and this repo used
  directly as a GitHub Action (`ruzickap/megalinter-custom-flavor-my-repos@main`).

## Two different MegaLinter configs - do not confuse them

- `megalinter-custom-flavor.yml` = the **flavor definition**: the list of
  linters baked into the built Docker image.
- `.mega-linter.yml` = config for **linting this repo's own files** in CI
  (arguments, disabled linters, filters). Sibling tool configs: `.rumdl.toml`,
  `lychee.toml`, `.checkov.yml`. Edit those, don't restate their contents.

## Changing the embedded linter set (important workflow)

The linter list is **duplicated** across `megalinter-custom-flavor.yml`,
`action.yml`, and the "Embedded linters" list in `README.md`. Do not hand-edit
them individually. Instead:

1. Edit the `linters:` list in `megalinter-custom-flavor.yml`.
2. Run `npx mega-linter-runner --custom-flavor-setup` to propagate the change to
   the other files.
3. Commit, then release to rebuild the image (see release flow below).

`.mega-linter.yml` sets `FAIL_IF_MISSING_LINTER_IN_FLAVOR: true`, so a linter
enabled for CI but absent from the running flavor fails the build.

`.mega-linter.yml` uses `# keep-sorted start` / `# keep-sorted end` markers -
keep entries within those blocks alphabetically sorted.

## Version pinning

- `MEGALINTER_VERSION` in `.github/workflows/megalinter-custom-flavor-builder.yml`
  is the source of truth for which MegaLinter version the image is built from.
  **Renovate** bumps it (as a `feat` commit). Corresponding pinned tags also
  appear in `action.yml` (`...megalinter-custom-flavor:v9.6.0`) and in workflow
  action SHAs - Renovate keeps these in sync; don't hand-bump.
- No `:latest` tag is published; consumers always reference an immutable version.

## Release / build flow (no PAT required)

1. Renovate bumps `MEGALINTER_VERSION` (`feat`) and merges to `main`.
2. `release-please.yml` (`release-type: simple`) opens/updates a release PR.
3. Merging the release PR publishes a GitHub Release.
4. `release-please.yml` then calls `megalinter-custom-flavor-builder.yml` (a
   reusable workflow) to build the image and push it to ghcr.io, because a
   release created by `GITHUB_TOKEN` cannot trigger workflows on its own. Can
   also be run via `workflow_dispatch`.
5. `ghcr-cleanup.yml` (monthly) keeps the 2 newest version tags and deletes
   untagged manifests.

Do not hand-edit versions or `CHANGELOG.md` (generated; excluded from all
linters/link checks).

## CI quirks (`.github/workflows/mega-linter.yml`)

- CI lints this repo using its **own custom flavor image** via the local
  action (`uses: ./` -> `action.yml` -> the ghcr.io image this repo builds), so
  the flavor produced and the flavor running CI are the same.
- Runs only on **branches other than `main`**, skipped for `chore/renovate/*`
  and `release-please--*` branches (unless `workflow_dispatch`), and only lints
  **changed `**/*.md`** files (plus extracts bash from markdown code blocks for
  validation). Pushing markdown to a feature branch is what triggers linting.

## Notes

- Sole code owner / reviewer: `@ruzickap` (`.github/CODEOWNERS`).
