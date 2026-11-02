# security-workflows

Reusable GitHub Actions workflow that runs osv-scanner, Semgrep and CodeQL on a repository and imports the results into DefectDojo.

## Usage

```yaml
name: Security

on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: "17 4 * * 1"
  workflow_dispatch:

permissions: {}

jobs:
  scan:
    permissions:
      contents: read
      security-events: write
    uses: cletqui/security-workflows/.github/workflows/scan.yml@<commit sha> # v1.0.0
    with:
      product: myrepo
      dojo_url: ${{ vars.DOJO_URL }}
      osv_lockfile: bun.lock
      codeql_language: javascript-typescript
    secrets:
      DOJO_TOKEN: ${{ secrets.DOJO_TOKEN }}
      CF_ACCESS_CLIENT_ID: ${{ secrets.CF_ACCESS_CLIENT_ID }}
      CF_ACCESS_CLIENT_SECRET: ${{ secrets.CF_ACCESS_CLIENT_SECRET }}
```

## Inputs

| Input | Default | Meaning |
|---|---|---|
| `product` | required | DefectDojo asset name |
| `product_type` | `apps` | DefectDojo organization |
| `dojo_url` | required | DefectDojo base URL, typically `${{ vars.DOJO_URL }}` |
| `min_severity` | `High` | lowest severity imported |
| `osv_lockfile` | empty | lockfile for osv-scanner, empty skips it |
| `semgrep_configs` | `p/typescript p/javascript p/nodejs p/owasp-top-ten` | space separated Semgrep rule packs |
| `codeql_language` | empty | CodeQL language, empty skips it |
| `codeql_build_mode` | `none` | CodeQL build mode |
| `codeql_upload` | `always` | set to `never` when GitHub's CodeQL default setup is enabled, so the analysis still runs and reaches DefectDojo without being uploaded to Code Scanning |

The import runs on push, schedule and manual runs, never on pull requests.
