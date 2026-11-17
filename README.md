# wakolab-design-systems-standard
Shared standards and contribution guidelines for reusable open-source design systems, documented for humans and agents.

## Status

This repository holds **authoring and documentation directions** for WakoLab design systems; it ships no code that other repositories import. The kit policy (K1–K7) was accepted by midas ([decision](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6#issuecomment-6933743616)) and is applied in [DIRECTIONS](docs/DIRECTIONS.md). Delivery status per system is in the [catalogue](docs/CATALOGUE.md).

## What this repository is, and is not

| It is | It is not |
| --- | --- |
| Directions for writing a new design system: colour, font and logo configuration, components and states, layout, compositions, motion, accessibility of shipped defaults | A runtime, a build package, a shared component library or a required checkout |
| The place for the extraction matrix and the independence check used to create the system repositories | A dependency of any system repository |

Each system repository (Console, Folio, Marquee) is **autonomous**: it can be cloned alone and used in an external project. If several systems need the same component, each keeps its own local copy, with its provenance noted. Links from a system's README to this repository are documentation, not a dependency.

## Documents

| Document | Content |
| --- | --- |
| [docs/DIRECTIONS.md](docs/DIRECTIONS.md) | Directions for a design-system kit (accepted kit policy) |
| [docs/CHECKLIST.md](docs/CHECKLIST.md) | Conformance checklist |
| [docs/CATALOGUE.md](docs/CATALOGUE.md) | Systems and their real delivery status |
| [template/TEMPLATE.md](template/TEMPLATE.md) | Copyable minimal repository template |
| [docs/CONFIGURATION.md](docs/CONFIGURATION.md) | Public specification of the configuration file (colours, fonts, logos, system-declared roles), with a [synthetic example](docs/examples/design-config.example.json) |
| [docs/EXTRACTION_MATRIX.md](docs/EXTRACTION_MATRIX.md) | Source in the WakoLab frontend → autonomous copy in each system repository → adaptation needed |
| [docs/KIT_PROPOSAL.md](docs/KIT_PROPOSAL.md) | The kit proposal accepted as K1–K7, with its rationale and review history (#6) |
| [docs/INDEPENDENCE_CHECK.md](docs/INDEPENDENCE_CHECK.md) | How to prove that a system works from an isolated clone |

## Licence

MIT (see [LICENSE](LICENSE)) for original code and documentation. Third-party material keeps its own licence and notices.

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). Agents also read [AGENTS.md](AGENTS.md) and [AGENTS-SYNC.md](AGENTS-SYNC.md).
