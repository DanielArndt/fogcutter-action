<p align="center">
  <img src="assets/logo.svg" alt="fogcutter-action logo: a small tugboat cutting through fog" width="160">
</p>

# fogcutter-action

[![CI](https://github.com/DanielArndt/fogcutter-action/actions/workflows/ci.yml/badge.svg)](https://github.com/DanielArndt/fogcutter-action/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/DanielArndt/fogcutter-action)](https://github.com/DanielArndt/fogcutter-action/releases)
[![License](https://img.shields.io/github/license/DanielArndt/fogcutter-action)](LICENSE)

GitHub Action that runs [fogcutter](https://github.com/DanielArndt/fogcutter) to check whether external blockers (GitHub issues and PRs, releases, Snapcraft channels, HTTP endpoints, upstream dependency versions, commands) are resolved, and triggers follow-up actions when they unblock.

The Action downloads a released fogcutter binary, verifies it against the release checksums, and runs it. It does not need Go on the runner. See the [fogcutter README](https://github.com/DanielArndt/fogcutter#readme) for the blocker config format and the available sources and actions.

## Usage

```yaml
name: Check External Blockers
on:
  schedule:
    - cron: "0 8 * * *"
  workflow_dispatch:

permissions:
  contents: read
  issues: write
  actions: write

jobs:
  fogcutter:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: DanielArndt/fogcutter-action@v1
        with:
          config: .github/blockers.yaml
          scan: "."
          scan_issues: "true"
```

Pin to a full commit SHA instead of `@v1` if you want fully reproducible runs. `@v1` is a floating tag that always points at the latest `1.x.y` release.

## Inputs

| Input | Default | Description |
| :--- | :--- | :--- |
| `config` | `.github/blockers.yaml` | Path to the blockers YAML configuration file |
| `scan` | | Comma-separated paths to scan for inline `BLOCKER(...)` or `TODO(blocked-by:...)` annotations |
| `scan_issues` | `false` | Scan open issues in the target repository for blocker annotations |
| `repo` | `$GITHUB_REPOSITORY` | Target repository (`owner/name`) for follow-up actions |
| `state_file` | | Path to a JSON file used to persist triggered blocker IDs |
| `dry_run` | `false` | Evaluate blockers and preview follow-up actions without side effects |
| `fail_on_resolved` | `false` | Fail the step if any blocker is resolved |
| `version` | see below | fogcutter release to run: `0.1.0`, `v0.1.0` or `latest` |
| `github_token` | `${{ github.token }}` | Token used to check issues and releases and to run follow-up actions |

### Choosing the fogcutter version

Each release of this Action has a default `version` that it was tested with, so a given Action version runs a known fogcutter version. A daily workflow opens a PR whenever fogcutter publishes a newer release, and merging it ships a new patch release of the Action.

Set `version` to pin a specific CLI release, or to `latest` to always run the newest one:

```yaml
- uses: DanielArndt/fogcutter-action@v1
  with:
    version: latest
```

## Outputs

| Output | Description |
| :--- | :--- |
| `total_blockers` | Number of blockers evaluated |
| `resolved_count` | Number of blockers that are resolved |
| `has_resolved` | `true` if at least one blocker is resolved |
| `resolved_json` | JSON array of the resolved blockers |

## Supported runners

Linux, macOS and Windows runners on amd64 and arm64. The Action runs on the matching fogcutter release archive and checks its SHA-256 against the release's `checksums.txt`. It fails if the archive is missing from the release or the checksum does not match.

## Versioning

This Action is versioned independently of the fogcutter CLI. Breaking changes to the inputs or outputs bump the major version and create a new floating major tag.

## License

[Apache-2.0](LICENSE)
