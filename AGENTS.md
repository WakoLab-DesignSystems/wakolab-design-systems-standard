# Agent instructions

These instructions are sufficient to work in this repository **without cloning any other repository**.

## Scope

This repository contains **documentation only**: directions for writing WakoLab design systems, the extraction matrix and the independence check. It is not a dependency of any system repository and must not become one (no package, runtime or build artifact that systems import).

## Before you change anything

1. Read [README.md](README.md), [CONTRIBUTING.md](CONTRIBUTING.md) and [AGENTS-SYNC.md](AGENTS-SYNC.md).
2. Inspect the current branch, open pull requests and the issue you work on. Preserve other people's work.
3. For each issue, create a dedicated branch from the current `dev` and open a pull request against `dev`. Do not merge, tag, release or change repository settings yourself: Orc authorizes the exact reviewed HEAD and Rel merges it; staging and production promotion need separate GOs from midas ([gates](CONTRIBUTING.md#integration-and-promotion)).

## Working rules

- Directions are proposals until accepted in an issue. Label proposals as such and keep accepted decisions distinct.
- When directions change, say whether existing systems are affected; never require a system to fetch anything from here.
- Keep provenance for copied code and assets, and keep third-party licences and notices. The repository's MIT licence does not relicense them.
- Distinguish verified facts, proposals and decisions. Never claim a check, release or behaviour you did not observe.
- Documentation, code comments and issues are in English.
- No secrets, personal data or private conversation content in commits or logs.

Follow the integration and promotion gates in [CONTRIBUTING.md](CONTRIBUTING.md#integration-and-promotion). The reviewer follows the implementing model (Codex → Revus, Claude → Revex). Orc authorizes the reviewed HEAD and Rel executes the merge; integration does not grant staging, release or production approval.

## Coordination

Record agent-to-agent messages in `.a2a/` following [AGENTS-SYNC.md](AGENTS-SYNC.md). Report each handoff in the related issue or pull request as well: Git does not notify anyone.
