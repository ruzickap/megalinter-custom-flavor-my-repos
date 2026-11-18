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

1. Edit the `linters:` list in `megalinter-custom-flavor.yml`. It is the only
   copy of the list; `README.md` links to it.
2. Merge to `main`: that rebuilds the image (see the build flow below).

Do not run `npx mega-linter-runner --custom-flavor-setup`: it overwrites
`action.yml` (with a `:latest` tag that is never published), `README.md` and
the builder workflow with upstream templates.

`.mega-linter.yml` sets `FAIL_IF_MISSING_LINTER_IN_FLAVOR: true`, so a linter
enabled for CI but absent from the running flavor fails the build (unless all
missing linters are `REPOSITORY_*` ones).

`.mega-linter.yml` uses `# keep-sorted start` / `# keep-sorted end` markers -
keep entries within those blocks alphabetically sorted.

## Version pinning

- `MEGALINTER_VERSION` in `.github/workflows/megalinter-custom-flavor-builder.yml`
  is the source of truth for which MegaLinter version the image is built from.
  **Renovate** bumps it (as a `fix`/`feat`/`feat!` commit) without the `v`
  prefix; the builder adds `v` back because upstream builder image tags use it.
  The `custom-builder` action pin in the same file is grouped with it into one
  Renovate PR (`groupName: "MegaLinter"` in
  `my-git-projects/.github/renovate-global.json5`).
- The built image tag (`v<version>`) and digest are pinned in `action.yml` and
  bumped by Renovate's `github-actions` manager (no `# renovate:` comment there,
  or the regex manager would strip the `v`); don't hand-bump.
- No `:latest` tag is published. `@main` follows the pin in `action.yml`;
  consumers who need a fixed image pin a commit SHA.

## Build / release flow (no PAT required)

1. Renovate bumps `MEGALINTER_VERSION` and merges it to `main`.
2. A push to `main` that changes `megalinter-custom-flavor.yml` or the builder
   workflow runs `megalinter-custom-flavor-builder.yml`: it builds the
   linux/amd64 image and pushes `v<version>` to ghcr.io. It can also be run via
   `workflow_dispatch`.
3. Renovate then bumps the image tag and digest in `action.yml`.
4. `release-please.yml` (`release-type: simple`) only maintains `CHANGELOG.md`
   and GitHub Releases; it does not build anything.

Build soon after a bump: the upstream builder pins exact Alpine package versions
(e.g. `go`), which Alpine deletes at its next update, so an older MegaLinter
version fails to rebuild until the next MegaLinter release.

Old images are never deleted: older commits and tags of `action.yml` still pin
them, and public ghcr.io storage is free.

Do not hand-edit versions or `CHANGELOG.md` (generated; excluded from all
linters/link checks).

## CI quirks (`.github/workflows/mega-linter.yml`)

- CI lints this repo with the image pinned in `action.yml` (`uses: ./`). Until
  a new build lands and Renovate bumps that pin, the image can lag behind
  `megalinter-custom-flavor.yml`.
- It runs on every push to a branch other than `main`, including Renovate
  branches (so an `action.yml` image bump is tested before it merges), but not
  on `release-please--*` branches (unless `workflow_dispatch`).
- MegaLinter lints the whole repo. Bash code blocks from changed Markdown files
  are also extracted into `bash_commands_from_md.sh` and checked.
- `mega-linter.yml`, `release-please.yml` and the other shared files are copied
  from `my-git-projects/gh-repo-defaults/` by multi-gitter;
  `multi-gitter-run-script.sh` swaps in `uses: ./` and drops the Renovate-branch
  skip for this repo. Change them there, not here.

## Notes

- Sole code owner / reviewer: `@ruzickap` (`.github/CODEOWNERS`).
