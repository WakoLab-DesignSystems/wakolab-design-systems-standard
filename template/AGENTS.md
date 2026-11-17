# Agent instructions

These instructions are sufficient to work in this repository **without cloning any other repository**.

## Where things are

| Need | Location |
| --- | --- |
| What the kit contains and its status | [README.md](README.md) |
| Design rules | [docs/rules/](docs/rules/README.md) |
| Defaults and configuration schema | [config.default.json](config.default.json), [config.schema.json](config.schema.json) |
| Example configuration | [examples/config/design-config.json](examples/config/design-config.json) |
| Manifest | [design-system.json](design-system.json) |
| Copied code and its sources | [docs/PROVENANCE.md](docs/PROVENANCE.md) |

## Rules

- **Autonomy.** Do not add a dependency, submodule, script or path that requires another WakoLab or WakoLab-DesignSystems repository. Copy what you need and record its provenance. Declare third-party packages.
- **Configuration.** Apply consumer values exactly as written; omitted roles keep their own defaults (only exception: `action-pressed` uses the effective `action-hover`). No colour derivation, contrast warnings, rejection or approval of consumer choices.
- **Defaults are verified**, never consumer values.
- **No product logic.** Examples use synthetic data.
- **Assets.** Add no font, icon or image whose licence and provenance are not recorded.
- Work on an issue branch from `dev` and open a pull request to `dev`; follow the gates in [CONTRIBUTING.md](CONTRIBUTING.md).
- Distinguish verified facts, proposals and decisions; never claim a check you did not run.
- English for code, documentation and issues. No secrets or personal data.

## Using {{SYSTEM_NAME}} in another project

{{REPLACE: once the implementation exists: how to add the package, render inside the system root, pass the configuration and assetBase}}

## Coordination

Record agent-to-agent messages in `.a2a/` following [AGENTS-SYNC.md](AGENTS-SYNC.md), and report handoffs in the related issue or pull request.
