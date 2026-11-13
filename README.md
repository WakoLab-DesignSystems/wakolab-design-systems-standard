# wakolab-design-systems-standard
Shared standards and contribution guidelines for reusable open-source design systems, documented for humans and agents.

## Status

Bootstrap only. This repository holds **authoring and documentation directions** for WakoLab design systems; it ships no code that other repositories import. The directions are a proposal until accepted (tracked in [#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1)).

## What this repository is, and is not

| It is | It is not |
| --- | --- |
| Directions for writing a new design system: colour, font and logo configuration, components and states, layout, compositions, motion, accessibility of shipped defaults | A runtime, a build package, a shared component library or a required checkout |
| The place for the extraction matrix and the independence check used to create the system repositories | A dependency of any system repository |

Each system repository (Console, Folio, Marquee) is **autonomous**: it can be cloned alone and used in an external project. If several systems need the same component, each keeps its own local copy, with its provenance noted. Links from a system's README to this repository are documentation, not a dependency.

## Documents

| Document | Content |
| --- | --- |
| [docs/DIRECTIONS.md](docs/DIRECTIONS.md) | Directions for a new system (proposal) |
| [docs/EXTRACTION_MATRIX.md](docs/EXTRACTION_MATRIX.md) | Source in the WakoLab frontend → autonomous copy in each system repository → adaptation needed |
| [docs/INDEPENDENCE_CHECK.md](docs/INDEPENDENCE_CHECK.md) | How to prove that a system works from an isolated clone |

## Licence

MIT (see [LICENSE](LICENSE)) for original code and documentation. Third-party material keeps its own licence and notices.

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). Agents also read [AGENTS.md](AGENTS.md) and [AGENTS-SYNC.md](AGENTS-SYNC.md).
