# Agent Permission Diff Gate

PR guardrail for GitHub Actions / MCP / agent permission creep.

This action compares permission capabilities between base and head, then warns
or fails when capability scope expands.

## Install

Pin to a release tag:

```yaml
- name: Agent Permission Diff Gate
  uses: eivindsjursen-lab/permission-diff-gate@v0.1.0
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
- `mode` (`warn` or `fail`, default `warn`)
- `policy_level` (`lenient` / `standard` / `strict`, default `standard`)
- `approval_label` (default `agent-scope-approved`)
- `allowlist_path` (optional path to allowlist YAML)
- `config_paths` (JSON array of glob patterns)

## Outputs

- `result` (`pass`, `warn`, `fail`, `skipped`)
- `confidence` (`low`, `med`, `high`)
- `reason_codes` (JSON array)
- `findings_count` (number)

## Example configs

See `examples/permission-diff-demo/` for:

- safe config
- risky config
- allowlist example

## Source of Truth

This repository is a standalone distribution mirror.
Development source of truth lives in:

- `eivindsjursen-lab/gates-suite`
- `packages/agent-permission-diff-gate`

