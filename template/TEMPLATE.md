# Using this template

This folder is a **copyable starting point** for a new design-system repository ([DIRECTIONS](../docs/DIRECTIONS.md), [CHECKLIST](../docs/CHECKLIST.md)). Copy it; never depend on it. Nothing here builds or runs: implementation, commands and deliverables are written in the new repository.

## Steps

1. An owner creates `wakolab-design-systems-<system>` (or accepts a transfer). Copy the content of this folder to its root, except this `TEMPLATE.md`.
2. Replace every placeholder:

   | Placeholder | Meaning |
   | --- | --- |
   | `{{SYSTEM}}` | System identifier, lowercase (for example `console`) |
   | `{{SYSTEM_NAME}}` | Display name (for example `Console`) |
   | `{{IDENTITY}}` | One sentence describing the system's look and composition |
   | `{{STANDARD_SHA}}` | Full commit SHA of this standard repository that the system follows |
   | `{{REPLACE}}` | A value to fill in (colours, font names, paths) |

3. Record the standard commit you copied from in `docs/PROVENANCE.md` and the README (`{{STANDARD_SHA}}`).
4. Create `.a2a/project.jsonl` with one initialization message using **new** identifiers, and fill in the record at the end of `AGENTS-SYNC.md`.
5. Fill `config.default.json` with the system's defaults, add any system-declared roles to it and to `config.schema.json` ([CONFIGURATION §2.1](../docs/CONFIGURATION.md#21-system-declared-roles)), and replace the example configuration and its files with synthetic ones.
6. Write `docs/rules/`, then the implementation, demo and checks. Record each required target (HTML file, Docker, `.fig`) as delivered or not delivered yet in the README.
7. Run the [checklist](../docs/CHECKLIST.md) in the first import PR, then add the system to the [catalogue](../docs/CATALOGUE.md).

## Files

| File | Purpose |
| --- | --- |
| `README.md` | Human entry point: status, kit contents, start, configuration, limits |
| `AGENTS.md` | Agent entry point: where things are and what not to do |
| `CONTRIBUTING.md` | Adapted from Console at `018fe3214e0a118162d202c2f39f1236f37b1936`: generic rules, dev-first workflow, reviewer routing and staging/production gates (source and adaptations recorded in `docs/PROVENANCE.md`) |
| `LICENSE` | MIT, copied from this repository |
| `AGENTS-SYNC.md`, `.a2a/tasks/.gitkeep` | A2A convention; identifiers to create |
| `THIRD_PARTY_NOTICES.md`, `docs/PROVENANCE.md` | Third-party licences; provenance of the starting files (pre-filled) and of later copies. Keep it when copying |
| `design-system.json` | Recommended manifest for agents |
| `config.default.json`, `config.schema.json` | Shipped defaults and the file schema (common roles; add declared roles) |
| `examples/config/design-config.json` | Example configuration; add its synthetic font and logo files next to it |
| `docs/rules/README.md` | Index of the design-rule pages |
