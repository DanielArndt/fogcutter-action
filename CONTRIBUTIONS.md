# Contributing to fogcutter-action

Thanks for your interest in contributing! Bug fixes, feature suggestions and documentation improvements are all welcome. The fogcutter CLI itself lives in [DanielArndt/fogcutter](https://github.com/DanielArndt/fogcutter); this repository only contains the GitHub Action that runs it.

---

## Commit Message Guidelines

This project follows the **[Conventional Commits](https://www.conventionalcommits.org/)** specification (v1.0.0). Commit messages and pull request titles, whether written by people or by automated agents, must use this format.

### Commit Format

```text
<type>(<optional scope>): <description>

[optional body]

[optional footer(s)]
```

### Commit Types

| Type | Description |
| :--- | :--- |
| `feat` | A new feature or input/output of the Action |
| `fix` | A bug fix |
| `docs` | Documentation-only changes |
| `style` | Formatting and whitespace (no functional changes) |
| `refactor` | Code restructuring that neither fixes a bug nor adds a feature |
| `perf` | Performance improvements |
| `test` | Adding or updating tests and fixtures |
| `build` | Build system and packaging changes |
| `ci` | Continuous integration and deployment configuration |
| `chore` | Routine tasks and repo maintenance |
| `revert` | Reverting a previous commit |

### Commit Rules

1. **Imperative mood**: write `add version input`, not `added` or `adds`.
2. **Lowercase**: start the description with a lowercase letter.
3. **No trailing period**.
4. **Length**: keep the subject line under 72 characters.
5. **Breaking changes**: append `!` before the colon or add a `BREAKING CHANGE:` footer. Removing or renaming an input or output is a breaking change.

### Examples

- `feat: add version input`
- `fix: verify the archive checksum on Windows runners`
- `fix(deps): bump default fogcutter version to 0.2.0`
- `docs: document the supported runners`

---

## Pull Requests and Merging

- **PR titles are validated** by the `Validate PR Title` workflow against the types and rules above.
- **PRs are squash-merged.** The PR title becomes the commit on `main`; rebase merging is also allowed and merge commits are disabled.
- **`main` is protected**: changes go through a PR with linear history, and `lint`, every `test (<os>)` job and `Validate PR Title` must pass.
- Head branches are deleted automatically after merge.

---

## Development and Testing

The Action is a composite action (`action.yml`) written in Bash. There is no build step.

- **Lint**: CI runs [actionlint](https://github.com/rhysd/actionlint) on the workflows. Locally: `docker run --rm -v "$PWD:/repo" --workdir /repo rhysd/actionlint:1.7.12`.
- **Test**: CI runs the Action from the checkout (`uses: ./`) in dry-run mode on Linux, macOS and Windows, amd64 and arm64, against `testdata/blockers.yaml`, and checks the outputs and the `fail_on_resolved` behavior. Add cases to the workflow and fixture when you change behavior.
- **Pin actions** to full commit SHAs with a version comment (for example `# v7.0.1`); Dependabot keeps them current.
- **Scripts must run under Git Bash on Windows** as well as Linux and macOS. Avoid tools or flags that are not available in all three, and be careful with backslashed paths.

---

## Releases and Versioning

Releases are automated with **[Release Please](https://github.com/googleapis/release-please)**, which reads the Conventional Commits on `main`. The Action is versioned independently of the fogcutter CLI.

1. `fix` produces a patch release, `feat` a minor release and a breaking change a major release.
2. Release Please maintains a Release PR that updates `CHANGELOG.md` and `.release-please-manifest.json`. Merging it tags the release and creates a GitHub Release.
3. The workflow then moves the floating major tag (for example `v1`) to the new release.
4. When fogcutter publishes a release, a daily workflow opens a `fix(deps)` PR that bumps the default `version` input in `action.yml`. Merging it produces a patch release of the Action.

The Release PR and the version bump PR are opened with a GitHub App token so the required checks run on them.
