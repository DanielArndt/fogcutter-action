# AGENTS.md

Guidelines for AI coding agents (and humans) working in this repository. See [CONTRIBUTIONS.md](CONTRIBUTIONS.md) for the full contributing guide.

## Project overview

`fogcutter-action` is a composite GitHub Action that downloads a released [fogcutter](https://github.com/DanielArndt/fogcutter) binary, verifies its checksum and runs it. It is versioned independently of the CLI.

| Path | Purpose |
| :--- | :--- |
| `action.yml` | The composite action: install step (download and verify) and run step |
| `testdata/blockers.yaml` | Fixture used by CI |
| `.github/workflows/ci.yml` | actionlint and a runner matrix that exercises the action |
| `.github/workflows/bump-fogcutter.yml` | Opens a PR when a new fogcutter release is published |
| `.github/workflows/release-please.yml` | Releases and the floating major tag |

## Testing

There is no build. CI lints with actionlint and runs the action in dry-run mode on Linux, macOS and Windows (amd64 and arm64). Run actionlint locally before proposing a change:

```bash
docker run --rm -v "$PWD:/repo" --workdir /repo rhysd/actionlint:1.7.12
```

Scripts in `action.yml` must work under Git Bash on Windows as well as Linux and macOS. When behavior changes, extend `testdata/blockers.yaml` and the CI assertions.

## Commit messages and PR titles (mandatory)

All commits and pull request titles **must** follow [Conventional Commits](https://www.conventionalcommits.org/). PRs are squash-merged and the PR title becomes the commit on `main`; a CI check rejects titles that do not conform.

- Format: `<type>(<optional scope>): <description>`
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`
- Description: imperative mood, lowercase first letter, no trailing period, under 72 characters
- Removing or renaming an input or output is a breaking change: mark it with `!` or a `BREAKING CHANGE:` footer

## Git workflow

- **Never commit directly to `main`.** It is protected; all changes go through a pull request.
- Start each piece of work on a new branch off an up-to-date `main`, named like `feat/version-input` or `fix/windows-checksum`.
- If you already have uncommitted work or are on another branch, use a separate `git worktree` for the new branch instead of switching.
- Keep one logical change per PR, and link the issue with `Closes #123` in the PR description.
- Do not rewrite history on shared branches or push to `main`.

## Conventions

- Every YAML file starts with the SPDX header used by the existing files (`SPDX-FileCopyrightText` and `SPDX-License-Identifier: Apache-2.0`).
- Pin every GitHub Action to a full commit SHA with a version comment; Dependabot keeps pins current.
- Keep the inputs and outputs in the README tables in sync with `action.yml`.
- Never commit secrets or tokens, and never log them.
