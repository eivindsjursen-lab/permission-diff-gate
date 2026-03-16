# Permission Diff Gate

PR guardrail that warns when a pull request widens GitHub Actions/agent
permissions.

## What it catches

- capability expansion between base and head commit
- high-risk/critical permission additions (policy-dependent)
- heuristic-only mappings when config lacks explicit permissions

## Example output shape

```text
result=warn
confidence=med
reason_codes=["WARN_CAPABILITY_EXPANSION","WARN_HEURISTIC_MAPPING"]
findings_count=2
```

Job Summary includes:

- what permission scope changed
- risk level for each finding
- concrete fix suggestions (explicit permissions, allowlist, approval label)

## Install (recommended)

Pin to immutable release tag:

```yaml
permissions:
  contents: read
  pull-requests: read

steps:
  - name: Permission Diff Gate
    uses: eivindsjursen-lab/permission-diff-gate@v0.1.4
    with:
      mode: warn
      policy_level: standard
```

Optional floating major tag:

```yaml
uses: eivindsjursen-lab/permission-diff-gate@v0
```

## Inputs

- `token` (default: `${{ github.token }}`)
- `mode` (`warn` or `fail`)
- `policy_level` (`lenient` / `standard` / `strict`)
- `approval_label` (default `agent-scope-approved`)
- `allowlist_path` (optional YAML allowlist path)
- `config_paths` (JSON array of glob patterns)
- `base_sha` (optional base SHA override, recommended for `workflow_dispatch`)
- `head_sha` (optional head SHA override, recommended for `workflow_dispatch`)

## Running mode notes

For `pull_request` events, default SHA resolution is usually enough.

For `workflow_dispatch` validation runs, pass explicit SHAs to avoid ambiguous
base/head selection:

```yaml
on:
  workflow_dispatch:
    inputs:
      base_sha:
        description: "Base commit SHA"
        required: true
      head_sha:
        description: "Head commit SHA"
        required: true

jobs:
  permission-diff:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: read
    steps:
      - uses: actions/checkout@v6
      - name: Permission Diff Gate
        uses: eivindsjursen-lab/permission-diff-gate@v0
        with:
          mode: warn
          policy_level: standard
          base_sha: ${{ inputs.base_sha }}
          head_sha: ${{ inputs.head_sha }}
```

## Outputs

- `result` (`pass`, `warn`, `fail`, `skipped`)
- `confidence` (`low`, `med`, `high`)
- `reason_codes` (JSON array)
- `findings_count` (number of findings)

## Config examples

See `examples/permission-diff-demo/`:

- `safe-config.yml`
- `risky-config.yml`
- `allowlist.yml`

## Trust / operations

- no external SaaS backend
- uses GitHub API with `GITHUB_TOKEN`
- runs in `mode=warn` by default
- rollback is one commit (remove the action step)

## Source of truth

This repository is the standalone distribution surface.
Development source of truth lives in:

- `eivindsjursen-lab/gates-suite`
- `packages/agent-permission-diff-gate`
