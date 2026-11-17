# Directions for a WakoLab design system

Status: **accepted kit policy.** midas accepted K1–K7 of the [kit proposal](KIT_PROPOSAL.md) ([decision](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6#issuecomment-6933743616)); this page states the resulting directions. The detailed rationale, history and review evidence stay in [KIT_PROPOSAL](KIT_PROPOSAL.md). M4 (reference defaults), the nested-root policy and external catalogue listing are **not** part of this decision. The earlier text of this page is at [`7703d84`](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/7703d841cd2082fcf4f5b8090ca0a910ffdc683b/docs/DIRECTIONS.md).

## 1. A system is an autonomous kit

- One repository per system. It can be cloned alone and used in any external project.
- No shared runtime, package, submodule or sibling checkout. A component needed by several systems is **copied** into each, with its provenance.
- Third-party dependencies are declared in the system's own repository.
- The system's README links to this repository for directions; that is documentation, not a dependency.
- The contract is **framework-neutral**: React, another framework, web components or plain CSS are all acceptable implementations.
- Each system renders only inside its own root element, with its mode on that root; its CSS, global CSS names, fonts, provider and event handlers do not affect content outside that root. The intended exceptions are a native modal dialog, which makes the whole page inert while it is open, and an explicitly enabled page-level shortcut option on a single root ([isolation contract](EXTRACTION_MATRIX.md#4-isolation-contract-e1-n6)).

## 2. What a kit contains

| Element | Status |
| --- | --- |
| `README.md`, `AGENTS.md`, `CONTRIBUTING.md`, `LICENSE`, third-party notices, `AGENTS-SYNC.md`, `.a2a/` | Required |
| `docs/rules/`: short design-rule pages (foundations, components and states, compositions, motion, accessibility, identity) | Required |
| `config.default.json`, `config.schema.json` and an example configuration with its font and logo files | Required |
| A demo source with a menu reaching a presentation page and every example screen | Required |
| **One self-contained HTML file** generated from that demo: presentation, examples by menu, configuration kept while navigating | Required target, delivered progressively |
| **Docker** short start serving the same demo over HTTP | Required target, delivered progressively |
| **`.fig` companion** library | Required target, delivered progressively, after a Figma feasibility spike; no account purchase without a decision |
| `design-system.json` manifest | Recommended |
| Checks on shipped defaults, independence and artwork | Required |

The [template](../template/TEMPLATE.md) gives a copyable starting point and the [checklist](CHECKLIST.md) the conformance criteria. The [catalogue](CATALOGUE.md) records each system's real delivery status.

**What a system describes:**
- **Foundations:** colour roles in light and dark, typography roles (`ui`, `display`, `prose`, `code`) with fallback stacks, spacing, radius, border, elevation (`flat`, `overlay`, `scrim`), focus, density **minimums**, icon rules.
- **Components** with their states (default, hover, focus-visible, pressed, selected, disabled, busy, invalid, as applicable), keyboard and pointer behaviour, accessible names and one example per state.
- **Compositions** defined by slots, not product content: shell, collection, detail, form, states (loading, empty, error).
- **Motion** recipes (feedback, overlay, content change) with reduced-motion alternatives.
- **Defaults** that give the system its own recognisable look.

## 3. Configuration

The public specification is [CONFIGURATION](CONFIGURATION.md). In short:
- `colors.light` and `colors.dark`: explicit values per colour role, including roles a system declares in addition to the common ones;
- `fonts.<role>`: a family and local font files;
- `logo`: local files and an accessible name.

`system` and `systemVersion` identify the file. Every customization key is optional and applied exactly as written; an omitted role keeps its own same-mode default (sole exception: `action-pressed` uses the effective `action-hover`). There is no colour derivation and no warning, rejection or approval of consumer choices.

## 4. Demo, HTML file and Docker

- **One demo source, two loading modes.** The offline HTML file embeds its resources, takes configuration and local font/logo files by user selection, and makes no network request. The HTTP demo (Docker or a local server) reads `/design/design-config.json` with `assetBase` `/design/` at each load.
- **Navigation and reload.** Navigating inside the open page keeps the mode, settings and selected files. A reload keeps only the mode, screen and scalar settings written in the URL hash; local files are selected again.
- **Generated HTML in Git.** A change adds a source commit, then a commit with the generated file, which embeds the source SHA. Corrections are appended the same way; published history is never rewritten. The file must be byte-identical when rebuilt from that source SHA with the locked toolchain.
- **Preview.** The README shows screenshots and links the HTML file for download. A hosted preview needs a separate publication decision (H6).

## 5. Verification

The project verifies its own **shipped defaults and examples**: contrast of declared pairs, accessibility checks on examples, keyboard, reduced motion and reflow. It never validates consumer choices. Each system also passes the [independence check](INDEPENDENCE_CHECK.md) and the [checklist](CHECKLIST.md).

## 6. Contributing and new systems

- **Contribute** to an existing system by fork and pull request to its `dev` branch.
- **Propose a new system** with an issue in this repository; Des and Sol review it; the maintainers decide; an owner creates the repository from the [template](../template/TEMPLATE.md) or accepts a transfer; the first import PR runs the [checklist](CHECKLIST.md); the system is then added to the [catalogue](CATALOGUE.md).
- **Maintainer rights** are write access to one repository, granted by an owner; never admin by default.
- Each system declares the standard version it follows as a commit SHA of this repository, in its README and, when present, in `design-system.json`.

## 7. Licence and provenance

MIT for original work. Third-party fonts, icons and other assets keep their own licences and notices. Copied code keeps a note of its source. An asset with unknown provenance is resolved, replaced or excluded, never accepted by discretion.
