# MegaLinter Custom Flavor: MyRepos

[![Build & Push MegaLinter Custom Flavor](https://github.com/ruzickap/megalinter-custom-flavor-my-repos/actions/workflows/megalinter-custom-flavor-builder.yml/badge.svg)](https://github.com/ruzickap/megalinter-custom-flavor-my-repos/actions/workflows/megalinter-custom-flavor-builder.yml)

This custom MegaLinter flavor aims to have an optimized Docker image size by
bundling only the linters used across
[my repositories](https://github.com/ruzickap).

It is built from official MegaLinter images, but is maintained on
<https://github.com/ruzickap/megalinter-custom-flavor-my-repos> by Petr Ruzicka.

## Embedded linters

The linters baked into the image are listed in
[`megalinter-custom-flavor.yml`](megalinter-custom-flavor.yml).

## How to use the custom flavor

Follow the
[MegaLinter installation guide](https://megalinter.io/latest/install-assisted/)
to create `.github/workflows/mega-linter.yml`, then replace the official
MegaLinter action with this custom flavor's GitHub Action:

```yaml
      - name: 💡 MegaLinter
        uses: ruzickap/megalinter-custom-flavor-my-repos@main
        env:
          GITHUB_COMMENT_REPORTER: false
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

The action pins its image by tag and digest, and Renovate keeps that pin
current. No `:latest` tag is published. `@main` follows the pin, so pin a commit
SHA instead if you need a fixed image.

## How the flavor is built and updated

This custom flavor is kept up to date with MegaLinter releases without any
Personal Access Token:

1. **Renovate** detects a new MegaLinter release and bumps `MEGALINTER_VERSION`
   in `.github/workflows/megalinter-custom-flavor-builder.yml`, merging it to
   `main`.
2. That push runs the `Build & Push MegaLinter Custom Flavor` workflow, which:
   - builds a Docker image with only the selected linters,
   - publishes it to GitHub Container Registry (ghcr.io) as
     `v<megalinter-version>` (e.g. `v10.2.0`).
3. **Renovate** then updates the image tag and digest pinned in `action.yml`.

release-please still maintains `CHANGELOG.md` and GitHub Releases, but a
release does not build anything.

To change the embedded linters, edit `megalinter-custom-flavor.yml` and merge
the change to `main`, which rebuilds the image. Do not run
`npx mega-linter-runner --custom-flavor-setup`: it overwrites `action.yml`,
this README and the builder workflow with upstream templates.

You can also run the `Build & Push MegaLinter Custom Flavor` workflow manually.
It only works soon after a MegaLinter bump: the upstream builder installs exact
Alpine package versions, which Alpine removes at its next update, so an older
MegaLinter version can't be rebuilt later.
