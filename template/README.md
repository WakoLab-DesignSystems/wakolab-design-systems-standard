# wakolab-design-systems-{{SYSTEM}}

{{SYSTEM_NAME}}: {{IDENTITY}}

## Status

{{REPLACE: for example "Extraction in progress under #1" or "Integrated in dev, accepted by midas at <SHA>"}}. Nothing is released or published unless stated here with its evidence.

## Kit

| Element | Status |
| --- | --- |
| Design rules (`docs/rules/`) | {{REPLACE: delivered / not delivered yet}} |
| Configuration (`config.default.json`, `config.schema.json`, `examples/config/`) | {{REPLACE}} |
| Demo with menu and presentation page | {{REPLACE}} |
| Single HTML file (offline) | {{REPLACE}} |
| Docker (HTTP demo) | {{REPLACE}} |
| `.fig` companion | {{REPLACE}} |

## Independent use

This repository is self-contained: it needs no other WakoLab or WakoLab-DesignSystems repository, submodule or sibling checkout. Third-party requirements: {{REPLACE: runtime and tools, with versions}}.

{{REPLACE: how to install, build and run the demo, once these commands exist and have been checked}}

## Configuration

Pass a `design-config.json` following the [configuration specification](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/{{STANDARD_SHA}}/docs/CONFIGURATION.md): explicit light and dark colours per role, your own font and logo files. Values are used exactly as written; omitted roles keep {{SYSTEM_NAME}}'s defaults. {{SYSTEM_NAME}} derives, checks and approves nothing. System-declared roles: {{REPLACE: none, or the list with their uses}}.

## Limits

{{REPLACE: excluded assets, unverified platforms, known differences}}

## Design directions

{{SYSTEM_NAME}} follows the [WakoLab design-system standard](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard) at `{{STANDARD_SHA}}`. That link is documentation only: this repository does not depend on it to build or run.

## Licence

MIT (see [LICENSE](LICENSE)) for original code and documentation. Third-party material keeps its own licence: see [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). Agents also read [AGENTS.md](AGENTS.md) and [AGENTS-SYNC.md](AGENTS-SYNC.md).
