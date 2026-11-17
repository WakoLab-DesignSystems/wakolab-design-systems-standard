# Contributing

Thank you for helping. This is an open-source project under the MIT licence. Contributing requires **no registration, token or reward agreement**; nothing in this repository creates one.

## How to contribute

1. Open or pick an issue, and agree the intended result in it before large changes.
2. Create a dedicated branch for the issue from the current `dev` and open a pull request against `dev`. Keep it focused, and explain what changed, why, and how you verified it.
3. Preserve other people's work, history and provenance. Never rewrite published history.
4. Obtain independent review from the reviewer that matches the implementing model (below); Orc authorizes integration of the exact reviewed HEAD and Rel performs the merge.

## Integration and promotion

This workflow governs contributions to this repository, not projects that consume the design system.

- Start each issue on a dedicated branch from the current `dev`; target `dev` for the pull request. Do not commit directly to `dev`, `staging` or `main`.
- **Review routing follows the implementing model:** work implemented by Codex (including Doc) is reviewed by Revus; work implemented by Claude is reviewed by Revex. A role name alone never selects the reviewer.
- Obtain independent review and the technical validations required by the issue. Orc (Product Owner and orchestrator) checks the evidence and explicitly authorizes the exact HEAD; Rel performs the merge. Any candidate change requires the affected validations to be checked again.
- After integration into `dev`, validate the identified integrated version in order: Sol (architecture/technical validation), Orc (criteria and evidence), then midas (Product Manager and CTO) for local human acceptance. Documentation-only changes need documentary checks, not unrelated product tests.
- Promotion to `staging` requires a separate GO from midas. The identified version is then validated in the staging environment. **If no staging environment is available, that gate is unmet:** report it as not done; it does not allow skipping to a release or production.
- Production requires a **separate production GO from midas**, given after the staging validation of the identified version.
- Production uses the existing `main` branch; do not create or rename it to `master`. Integration, human acceptance, release creation and deployment are separate events. Creating branches does not prove any of them. Package or site publication also needs explicit authorization.

## Rules

- **Keep the repository autonomous.** Do not add a dependency, submodule, script or path that requires another WakoLab or WakoLab-DesignSystems repository. Copy what you need, and note where it came from.
- **Do not judge consumer choices.** Configuration values are applied as written; checks cover this system's own defaults and examples.
- **Provenance and licences.** Code or assets copied from elsewhere keep a note of their source (repository, path and commit) and their original licence and notices. The MIT licence of this repository covers original work only; it does not relicense third-party material such as fonts or icons.
- **No secrets or personal data** in commits, issues or logs.
- **English** for code, documentation and issues.
- **Verification.** Run the checks this repository provides; for documentation, check links and consistency. Report what you could not verify.

## Agents

AI agents follow [AGENTS.md](AGENTS.md) and record agent-to-agent messages as described in [AGENTS-SYNC.md](AGENTS-SYNC.md).
