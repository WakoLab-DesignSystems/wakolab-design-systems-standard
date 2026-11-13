# Agent instructions

These instructions are sufficient to work in this repository **without cloning any other repository**.

## Scope

This repository contains **documentation only**: directions for writing WakoLab design systems, the extraction matrix and the independence check. It is not a dependency of any system repository and must not become one (no package, runtime or build artifact that systems import).

## Before you change anything

1. Read [README.md](README.md), [CONTRIBUTING.md](CONTRIBUTING.md) and [AGENTS-SYNC.md](AGENTS-SYNC.md).
2. Inspect the current branch, open pull requests and the issue you work on. Preserve other people's work.
3. Work on a branch and open a pull request against `main`; do not merge, tag, release or change repository settings unless the issue explicitly authorizes it.

## Working rules

- Directions are proposals until accepted in an issue. Label proposals as such and keep accepted decisions distinct.
- When directions change, say whether existing systems are affected; never require a system to fetch anything from here.
- Keep provenance for copied code and assets, and keep third-party licences and notices. The repository's MIT licence does not relicense them.
- Distinguish verified facts, proposals and decisions. Never claim a check, release or behaviour you did not observe.
- Documentation, code comments and issues are in English.
- No secrets, personal data or private conversation content in commits or logs.

## Coordination

Record agent-to-agent messages in `.a2a/` following [AGENTS-SYNC.md](AGENTS-SYNC.md). Report each handoff in the related issue or pull request as well: Git does not notify anyone.
