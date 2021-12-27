# hugo-action-build-audit
GitHub action to checkout, build, and audit a Hugo site

## Status

### Main

![test-build-audit](https://github.com/danielfdickinson/hugo-action-build-audit/actions/workflows/test-build-audit.yml/badge.svg)

### Pull Request

![test-build-audit PR](https://github.com/danielfdickinson/hugo-action-build-audit/actions/workflows/test-build-audit.yml/badge.svg?event=pull_request)

## Details

**NB**: Currently experimental and not for public consumption. Use at your own risk.

### Purpose

Build a Hugo website and [audit for silent errors](https://discourse.gohugo.io/t/audit-your-published-site-for-problems/35184/8). Optionally generate an artifact for use by other jobs in a GitHub Actions Workflow (e.g. for other tests or deployment).

### Inputs


| Input | Description | Req'd | Default |
|-------|-------------|-------|---------|
| checkout-fetch-depth | Fetch depth: Use 0 to fetch all history if using .GitInfo or .Lastmod | true | 0 |
| checkout-submodules | Fetch git submodules: false, true, or recursive | true | false |
| code-directory | Directory under which repo and modules will live | true | ${{ github.workspace }}/code |
| do-minify | If present, minify site using --minify during build | false | |
| hugo-cache-directory | Where to place the Hugo module cache, under the code-directory | true | hugo_cache
| hugo-env | Hugo environment (production, development, etc) | true | production |
| hugo-extended | Hugo Extended | true | true |
| hugo-version | Hugo Version | true | 0.89.4 |
| image-formats | Image formats to include in resource hash key | true | ['webp', 'svg', 'png', 'jpg', 'jpeg','gif', 'tiff', 'tif', 'bmp'] |
| output-directory | Location of the site output by Hugo, relative to the workspace | true | public |
| repo-directory | Where to checkout the repo, under the code-directory | true | repo |
| source-directory | Where the source for the site lives, within the repo | false | |
| upload-site-as | Artifact to create containing the Hugo site | false | |
| upload-site-filename | Filename for tarball of site to upload to artifact | true | hugo-site.tar |
| upload-site-retention | Retention period in days for Hugo site artifact | true | 1 |
| use-lfs | Use LFS when checking out out repo | true | false |

### Outputs

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
      - staging-**
    paths-ignore:
      - 'README.md'
      - 'LICENSE'
      - '.gitignore'
      - '.vscode'
      - 'scripts'
jobs:
  build-unminified-site:
    runs-on: ubuntu-20.04
    steps:
      - name: "Build Site with Hugo and Audit"
        uses: danielfdickinson/hugo-action-build-audit@v0.1.1
        with:
          upload-site-as: unminified-site
          use-lfs: true
  validate-unminified-html:
    needs: build-unminified-site
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
      - name: "Validate site HTML"
        uses: danielfdickinson/hugo-action-html-validate@v0.1.1
  check-links:
    needs: build-unminified-site
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
      - name: "Check internal links"
        uses: danielfdickinson/hugo-action-check-links@v0.1.1
        with:
          canonical-root: https://www.example.com/
```
