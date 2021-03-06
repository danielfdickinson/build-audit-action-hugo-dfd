# Hugo build-audit GitHub Action

GitHub action to checkout, build, and audit a Hugo site

## Status

[![test-build-audit](https://github.com/danielfdickinson/build-audit-action-hugo-dfd/actions/workflows/test-build-audit.yml/badge.svg)](https://github.com/danielfdickinson/build-audit-action-hugo-dfd/actions/workflows/test-build-audit.yml)

## GitHub repository

<https://github.com/danielfdickinson/build-audit-action-hugo-dfd>

### Purpose

Build a Hugo website and [audit for silent errors](https://discourse.gohugo.io/t/audit-your-published-site-for-problems/35184/8). Optionally generate an artifact for use by other jobs in a GitHub Actions Workflow (e.g. for other tests or deployment).

### Exclusion

Due to a bug in Hugo 0.97.0, Hugo version 0.97.0 is not supported by this action.

### Action inputs variables

| Input | Description | Req'd | Default |
|-------|-------------|-------|---------|
| base-url | Set the baseURL for the site (for this build only) | false | |
| checkout-fetch-depth | Fetch depth: Use 0 to fetch all history if using .GitInfo or .Lastmod | true | 0 |
| checkout-submodules | Fetch git submodules: false, true, or recursive | true | false |
| code-directory | Directory under which repo and modules will live | true | ${{ github.workspace }}/code |
| do-minify | If present, minify site using --minify during build | false | |
| hugo-cache-directory | Where to place the Hugo module cache, under the code-directory | true | hugo_cache
| hugo-env | Hugo environment (production, development, etc) | true | production |
| hugo-extended | Hugo Extended | true | true |
| hugo-version | Hugo Version | true | 'latest' |
| image-formats | Image formats to include in resource hash key | true | ['webp', 'svg', 'png', 'jpg', 'jpeg','gif', 'tiff', 'tif', 'bmp'] |
| output-directory | Location of the site output by Hugo, relative to the workspace | true | public |
| repo-directory | Where to checkout the repo, under the code-directory | true | repo |
| source-directory | Where the source for the site lives, within the repo | false | |
| upload-site-as | Artifact to create containing the Hugo site | false | |
| upload-site-filename | Filename for tarball of site to upload to artifact | true | hugo-site.tar |
| upload-site-retention | Retention period in days for Hugo site artifact | true | 1 |
| use-lfs | Use LFS when checking out out repo | true | false |

### Action outputs variables

None

### Sample usage

```yaml
name: test-build-on-pr
on:
  pull_request:
    types:
      - assigned
      - opened
      - synchronize
      - reopened
  push:
    branches:
      - main
jobs:
  build-unminified-site:
    runs-on: ubuntu-20.04
    steps:
      - name: "Build Site with Hugo and Audit"
        uses: danielfdickinson/build-audit-action-hugo-dfd@v0.2
        with:
          base-url: https://www.example.com/
          upload-site-as: unminified-site
          use-lfs: true
  validate-unminified-html:
    needs: build-unminified-site
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v3
      - name: "Validate site HTML"
        uses: danielfdickinson/validate-html-action-hugo-dfd@v0.2
  check-links:
    needs: build-unminified-site
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v3
      - name: "Check internal links"
        uses: danielfdickinson/link-check-action-hugo-dfd@v0.2
        with:
          canonical-root: https://www.example.com/
```
