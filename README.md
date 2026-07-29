# MegaLinter Custom Flavor: MyRepos

[![Build & Push MegaLinter Custom Flavor](https://github.com/ruzickap/megalinter-custom-flavor-my-repos/actions/workflows/megalinter-custom-flavor-builder.yml/badge.svg)](https://github.com/ruzickap/megalinter-custom-flavor-my-repos/actions/workflows/megalinter-custom-flavor-builder.yml)

This custom MegaLinter flavor aims to have an optimized Docker image size by
bundling only the linters used across
[my repositories](https://github.com/ruzickap).

It is built from official MegaLinter images, but is maintained on
<https://github.com/ruzickap/megalinter-custom-flavor-my-repos> by Petr Ruzicka.

## Embedded linters

- [ACTION_ACTIONLINT](https://megalinter.io/latest/descriptors/action_actionlint/)
- [ACTION_ZIZMOR](https://megalinter.io/latest/descriptors/action_zizmor/)
- [ANSIBLE_ANSIBLE_LINT](https://megalinter.io/latest/descriptors/ansible_ansible_lint/)
- [BASH_EXEC](https://megalinter.io/latest/descriptors/bash_bash_exec/)
- [BASH_SHELLCHECK](https://megalinter.io/latest/descriptors/bash_shellcheck/)
- [BASH_SHFMT](https://megalinter.io/latest/descriptors/bash_shfmt/)
- [CLOUDFORMATION_CFN_LINT](https://megalinter.io/latest/descriptors/cloudformation_cfn_lint/)
- [COPYPASTE_JSCPD](https://megalinter.io/latest/descriptors/copypaste_jscpd/)
- [DOCKERFILE_HADOLINT](https://megalinter.io/latest/descriptors/dockerfile_hadolint/)
- [GO_GOLANGCI_LINT](https://megalinter.io/latest/descriptors/go_golangci_lint/)
- [GO_REVIVE](https://megalinter.io/latest/descriptors/go_revive/)
- [HTML_DJLINT](https://megalinter.io/latest/descriptors/html_djlint/)
- [HTML_HTMLHINT](https://megalinter.io/latest/descriptors/html_htmlhint/)
- [JAVASCRIPT_ES](https://megalinter.io/latest/descriptors/javascript_eslint/)
- [JSON_JSONLINT](https://megalinter.io/latest/descriptors/json_jsonlint/)
- [JSON_NPM_PACKAGE_JSON_LINT](https://megalinter.io/latest/descriptors/json_npm_package_json_lint/)
- [JSON_PRETTIER](https://megalinter.io/latest/descriptors/json_prettier/)
- [JSON_V8R](https://megalinter.io/latest/descriptors/json_v8r/)
- [LATEX_CHKTEX](https://megalinter.io/latest/descriptors/latex_chktex/)
- [MARKDOWN_MARKDOWN_TABLE_FORMATTER](https://megalinter.io/latest/descriptors/markdown_markdown_table_formatter/)
- [MARKDOWN_RUMDL](https://megalinter.io/latest/descriptors/markdown_rumdl/)
- [PYTHON_BANDIT](https://megalinter.io/latest/descriptors/python_bandit/)
- [PYTHON_FLAKE8](https://megalinter.io/latest/descriptors/python_flake8/)
- [PYTHON_ISORT](https://megalinter.io/latest/descriptors/python_isort/)
- [PYTHON_MYPY](https://megalinter.io/latest/descriptors/python_mypy/)
- [PYTHON_NBQA_MYPY](https://megalinter.io/latest/descriptors/python_nbqa/)
- [PYTHON_PYLINT](https://megalinter.io/latest/descriptors/python_pylint/)
- [PYTHON_PYRIGHT](https://megalinter.io/latest/descriptors/python_pyright/)
- [PYTHON_RUFF](https://megalinter.io/latest/descriptors/python_ruff/)
- [PYTHON_RUFF_FORMAT](https://megalinter.io/latest/descriptors/python_ruff_format/)
- [REPOSITORY_BETTERLEAKS](https://megalinter.io/latest/descriptors/repository_betterleaks/)
- [REPOSITORY_CHECKOV](https://megalinter.io/latest/descriptors/repository_checkov/)
- [REPOSITORY_DEVSKIM](https://megalinter.io/latest/descriptors/repository_devskim/)
- [REPOSITORY_DUSTILOCK](https://megalinter.io/latest/descriptors/repository_dustilock/)
- [REPOSITORY_GITLEAKS](https://megalinter.io/latest/descriptors/repository_gitleaks/)
- [REPOSITORY_GIT_DIFF](https://megalinter.io/latest/descriptors/repository_git_diff/)
- [REPOSITORY_GRYPE](https://megalinter.io/latest/descriptors/repository_grype/)
- [REPOSITORY_KINGFISHER](https://megalinter.io/latest/descriptors/repository_kingfisher/)
- [REPOSITORY_OSV_SCANNER](https://megalinter.io/latest/descriptors/repository_osv_scanner/)
- [REPOSITORY_SECRETLINT](https://megalinter.io/latest/descriptors/repository_secretlint/)
- [REPOSITORY_SYFT](https://megalinter.io/latest/descriptors/repository_syft/)
- [REPOSITORY_TRIVY](https://megalinter.io/latest/descriptors/repository_trivy/)
- [REPOSITORY_TRIVY_SBOM](https://megalinter.io/latest/descriptors/repository_trivy_sbom/)
- [REPOSITORY_TRUFFLEHOG](https://megalinter.io/latest/descriptors/repository_trufflehog/)
- [RUBY_RUBOCOP](https://megalinter.io/latest/descriptors/ruby_rubocop/)
- [SPELL_CODESPELL](https://megalinter.io/latest/descriptors/spell_codespell/)
- [SPELL_LYCHEE](https://megalinter.io/latest/descriptors/spell_lychee/)
- [SQL_TSQLLINT](https://megalinter.io/latest/descriptors/sql_tsqllint/)
- [TERRAFORM_TERRAFORM_FMT](https://megalinter.io/latest/descriptors/terraform_terraform_fmt/)
- [TERRAFORM_TFLINT](https://megalinter.io/latest/descriptors/terraform_tflint/)
- [TSX_ESLINT](https://megalinter.io/latest/descriptors/tsx_eslint/)
- [TYPESCRIPT_ES](https://megalinter.io/latest/descriptors/typescript_eslint/)
- [XML_XMLLINT](https://megalinter.io/latest/descriptors/xml_xmllint/)
- [YAML_PRETTIER](https://megalinter.io/latest/descriptors/yaml_prettier/)
- [YAML_V8R](https://megalinter.io/latest/descriptors/yaml_v8r/)
- [YAML_YAMLLINT](https://megalinter.io/latest/descriptors/yaml_yamllint/)

## How to use the custom flavor

Follow the
[MegaLinter installation guide](https://megalinter.io/latest/install-assisted/)
to create `.github/workflows/mega-linter.yml`, then point the MegaLinter step at
this custom flavor. There are two equivalent ways to do it.

### Option A - reference the Docker image directly (recommended)

Replace the official MegaLinter action with this repo's pinned image via
`uses: docker://...`:

```yaml
      # renovate: datasource=docker depName=ghcr.io/ruzickap/megalinter-custom-flavor-my-repos/megalinter-custom-flavor
      - name: 💡 MegaLinter
        uses: docker://ghcr.io/ruzickap/megalinter-custom-flavor-my-repos/megalinter-custom-flavor:v9.6.0
        env:
          GITHUB_COMMENT_REPORTER: false
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Option B - use it as a GitHub Action

The repo also ships an `action.yml` that runs the same pinned image:

```yaml
      - name: 💡 MegaLinter
        uses: ruzickap/megalinter-custom-flavor-my-repos@main
        env:
          GITHUB_COMMENT_REPORTER: false
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

No `:latest` tag is published, so consumers always reference an immutable,
pinned version (kept in sync by Renovate).

## How the flavor is generated and updated

This custom flavor is kept up to date with MegaLinter releases without any
Personal Access Token:

1. **Renovate** detects a new MegaLinter release and bumps `MEGALINTER_VERSION`
   in `.github/workflows/megalinter-custom-flavor-builder.yml` as a `feat`
   commit, merging it to `main`.
2. **release-please** sees the `feat` commit and opens (or updates) a release
   pull request.
3. **You merge the release PR when ready**, which publishes a GitHub Release.
4. `release-please` then calls the `megalinter-custom-flavor-builder` reusable
   workflow (a release created by `GITHUB_TOKEN` cannot trigger workflows on its
   own), which:
   - builds a Docker image with only the selected linters,
   - publishes it to GitHub Container Registry (ghcr.io).

Image tags:

- `v<megalinter-version>` (e.g. `v9.6.0`): the MegaLinter version the image was
  built from. No `:latest` tag is published, so consumers always reference an
  immutable, reproducible version (kept in sync by Renovate).

To change the embedded linters, edit `megalinter-custom-flavor.yml`, run
`npx mega-linter-runner --custom-flavor-setup` to propagate the change to the
other files, then commit and create a release to rebuild. You can also run the
`Build & Push MegaLinter Custom Flavor` workflow manually to rebuild the
currently pinned MegaLinter version.
