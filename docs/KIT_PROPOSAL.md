# Distributable kit and contribution path: proposal

Status: **proposal by Sol for [#6](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6); nothing here is accepted or implemented.** It builds on the integrated standard (`dev` at `6dc2a7a`) and on Console as integrated and accepted by midas at `018fe3214e0a118162d202c2f39f1236f37b1936`. It changes nothing in Console, the WakoLab frontend or any repository setting.

Goal (midas): each design system is an **autonomous kit** that people and agents can understand, discover, try, customize and reuse in an external project.

## 1. Journey and minimal layout

**Journey.** Discover (README with screenshots) → try without installing (a single HTML file) → try with your own colours, fonts and logo (demo with configuration, Docker or Node) → reuse the code in an external app → optionally edit the library in Figma → contribute.

**Common contract versus implementation.** The common contract is framework-neutral: the configuration file and its schema, the colour/font/logo roles, the design rules (foundations, components and states, compositions, motion, accessibility), the neutral example screens and the checks on shipped defaults. A system's **implementation** is free: Console is React 19 + prebuilt CSS; a future system may use another framework, web components or CSS only, provided it ships the kit elements below. The standard must not require React.

**Proposed layout of a system repository** (Console paths at `018fe32` in the "Exists?" column):

| Path | Use and content | Source | Proposed status | Evidence it works | Exists in Console? |
| --- | --- | --- | --- | --- | --- |
| `README.md` | What the kit contains, capabilities, limits, screenshots, 3-step human start, links to everything below | Written | **Required** | Link check; steps reproduced from a clean clone | Yes; status line stale, no screenshots, no kit index |
| `AGENTS.md` | Discovery and use by an agent: where the config, rules, components and examples are; what not to do | Written | **Required** | An agent following only `AGENTS.md` reaches the demo and applies the example configuration | Yes ("Using Console in another project") |
| `CONTRIBUTING.md`, `LICENSE`, `THIRD_PARTY_NOTICES.md` + `licenses/`, `AGENTS-SYNC.md`, `.a2a/` | Collaboration base, licences, provenance | Existing | **Required** (unchanged) | Present; notices match declared third-party material | Yes |
| `design-system.json` | Small manifest: system id and version, **declared standard version**, entry points (config, rules, components, examples, demo), coverage, platforms | Written | **Recommended** (helps agents) | Validates against a schema in the standard; paths exist | No (README covers it in prose) |
| `config.default.json`, `config.schema.json` | Shipped defaults and the file contract ([CONFIGURATION](CONFIGURATION.md)) | Existing contract | **Required** | Schema validates defaults and example | Yes |
| `examples/config/` (or Console's `examples/public/design/`) | Example configuration: light/dark colours, a font file, logo files | Synthetic | **Required** | The demo loads it; values applied exactly | Yes |
| `docs/rules/` | Design rules in short pages: foundations, components and states, compositions, motion, accessibility, identity | Des, from the system's source and review | **Required (short)** | Des review; links from README and `AGENTS.md` | **No**: rules exist only as README prose and in the central proposal |
| Implementation folder (`src/` for Console) | Components, compositions, styles | Extracted or authored | Required for an implemented system | Build, type check, tests | Yes |
| `examples/demo/` | Interactive demonstration: a menu reaching every available screen, keeping mode and configuration | Built from the real components | **Required** | Playwright: every menu entry renders; mode and config kept across screens | Partly: `examples/neutral/` has all screens but navigation is by query string (midas's follow-up) |
| `<system>-kit.html` (generated) | One self-contained file: presentation (overview) **and** interactive demo, opens offline | Generated from `examples/demo/` | **Recommended** | Opens from `file://` with no network request; same checks as the demo | No |
| `docker/` (`Dockerfile`, `compose.yaml`) | Short start that serves the demo with an editable `design/` folder | Written | **Optional** | `docker compose up` from a clean clone; editing the folder and reloading shows the change | No |
| `design/figma/` | `.fig` library, plus a mapping from Figma components/variants to code | Des (with generated variables) | **Optional**, after a spike | Import in Figma; checklist in §3.5 | No |
| `docs/PROVENANCE.md`, `docs/PARITY.md` | Source mapping and parity evidence for extracted systems | Existing | Required when code is copied | Present and current | Yes |
| `tests/`, `scripts/` | Checks on shipped defaults, independence, artwork | Existing | **Required** | Run in the PR | Yes |

**Gaps to fill for Console, and only these:** `docs/rules/`; demo menu (already requested by midas); generated single-file kit HTML; README screenshots, kit index, status and build wording; optional `design-system.json`, Docker and Figma. Everything else exists.

## 2. Presentation, demonstration and Figma: one source

| Artefact | What it is | Single source |
| --- | --- | --- |
| Presentation | A guided overview: identity, foundations, components and states, compositions, configuration | An **Overview** screen of the demo, built with the real components. Des's central showcase (`WakoLab/wakolab` `docs/design/design-system-standard/showcase/`, a plain-HTML reimplementation) provides the outline and texts, not code |
| Interactive demo | The neutral screens plus the Overview, with a menu | `examples/demo/` |
| Single-file HTML | Presentation + demo in one offline file | Generated from the same demo by a build step |
| Figma library | Editable components, variants, styles and modes | Variables/styles **generated** from `config.default.json`; components drawn by Des and named after the code components and props |

Consistency rules:
- No hand-written second HTML implementation. The demo and the single file are builds of the same code.
- The menu lists every available screen, marks the active one, and keeps `mode` and the active configuration when navigating (in the URL hash, so it also works from `file://`).
- The single file embeds its source commit and the standard version; a check rebuilds it and compares before an accepted version is recorded.
- Figma follows the code: tokens are regenerated on each defaults change; component variants follow a committed mapping table; Des reviews drift at each minor version. Figma is never the source of truth for code.

## 3. Usage paths

1. **Download and open the HTML locally.** The single file inlines scripts, styles, default fonts and the example logo, and reads configuration from an in-page panel or a local file picker (no `fetch` from `file://`, which browsers block). Download it from the repository file view ("Download raw file"), then double-click.
2. **Preview from the README without publishing.** GitHub shows HTML files as source, not as a running page; the README must not link them as a "live demo". Proposed: committed screenshots (light, dark, configured) in the README, plus the downloadable single file. A hosted preview (GitHub Pages or similar) is a **publication** and waits for H6. Third-party "HTML preview" proxies are not recommended.
3. **Docker, including Windows with WSL.** A two-stage image (Node build, then a small static server) serves the built demo on `127.0.0.1:5190`, with `./design/` mounted so that editing `design-config.json`, fonts or logos and reloading shows the change; no rebuild and no file watching are needed because the demo loads the configuration at runtime. On Windows: Docker Desktop with the WSL 2 backend; clone inside the WSL file system (for example `~/src`) rather than under `C:\`; run `docker compose up` from a WSL terminal; open the URL in the Windows browser. **Not yet verified on Windows**: a human run is required before the README claims it.
4. **Use the code in an external app.** Unchanged from the integrated contract: clone and build, local tarball, or a Git dependency pinned by commit; import the stylesheet once; render inside the root; pass the configuration and `assetBase`. Already proven for Console by Des's external-consumer runs.
5. **Import the `.fig` in Figma.** Feasibility, as far as checked (sources: Figma documentation and forum, to be confirmed in a spike):
   - A `.fig` file is produced from Figma itself ("Save local copy") and can be imported back; there is no supported way to write `.fig` from code. A human with a Figma account must therefore make and export it.
   - Editable components, variants (component sets), text and effect styles and auto layout can be created by a **plugin** (Plugin API) or by hand.
   - Light/dark **modes** use variable collections; the number of modes per collection depends on the Figma plan, and Figma's own pages disagree on the current limits. Two modes are needed.
   - The REST Variables write API is restricted to Enterprise members; the Plugin API is not, so generation should use a small local plugin (or a token-import plugin) run by a human.
   - Human interventions: a Figma account and plan, running the plugin, drawing or adjusting components, exporting the `.fig`, and re-exporting after changes.
   - Spike exit criteria: variables with light/dark modes from `config.default.json`; colour, text and effect styles; Button with its variants and states; one composition with auto layout; import of the exported `.fig` into a fresh file keeps them editable.

## 4. Welcoming a new system

Three distinct things:

| Act | Who | Rights needed |
| --- | --- | --- |
| Contribute to an existing system | Anyone | None: fork and pull request to `dev`, normal review and Orc/Rel gates |
| Propose a new system | Anyone | None: an issue in the standard repository |
| Maintain a system in the organization | Named maintainers | Write access to that repository only, granted by an owner; **never admin by default** |

**Proposed path (simplified to four steps):**
1. **Proposal**: an issue in the standard repository with name, identity (screenshots), source and licence of every asset, framework, maintainer, and whether it is new work or an existing repository.
2. **Review and decision**: one combined review (Des: identity, coverage, neutrality; Sol: licence/provenance readiness and checklist gaps), then the maintainers' decision (midas, with Orc).
3. **Repository**: an owner creates `wakolab-design-systems-<name>` from the template (§5), or accepts a transfer of an existing repository. The proposer contributes through fork and pull request; write access only if named maintainer.
4. **Conformance and catalogue**: the first import PR runs the checklist in the normal pipeline; once integrated and accepted, a PR adds the system to a catalogue table in the standard README.

Removed from the initial idea: a separate "dossier" step (it is the proposal issue) and a separate conformance review (it is the first PR's review).

## 5. Conformance checklist

| Item | Automated check | Des review | Maintainers' decision |
| --- | --- | --- | --- |
| Autonomy | No dependency on another WakoLab repository; isolated clone builds and runs the demo | — | — |
| Documentation | README, AGENTS, CONTRIBUTING, rules pages present; links resolve | Rules are clear and complete for the components announced | — |
| Explicit configuration | Schema validates `config.default.json` and the example; the demo applies the example exactly; omitted keys keep defaults | — | — |
| Examples | Demo menu reaches every announced screen; no network call; neutral texts (no WakoLab product content) | Examples are neutral and representative | — |
| Licences and provenance | `LICENSE` is MIT; notices exist for every declared third-party package; artwork audit | — | Unclear provenance (accept, replace or exclude) |
| Components and rules announced | Every announced component has an example and is exported | Identity, states and coverage match the claims | Accepted exceptions (for example a parity exception) |
| Shipped defaults | Contrast and accessibility checks on the **defaults and examples only** | — | — |

The checks never evaluate a consumer's configuration values: only the system's own defaults and examples.

**Template:** recommended as a `template/` folder in this repository (copy, do not depend), holding the required files with placeholders. A separate template repository is only worth it when several systems are created; creating it is an owner action.

**Declared standard version:** each system declares the standard it follows, in README and `design-system.json`. Today a commit SHA (Console: `6dc2a7a`); SemVer tags of the standard later, if tagging is authorized. No central service or registry.

## 6. Minimal sequence

| Order | Task | Owner | Depends on |
| --- | --- | --- | --- |
| 1 | Decide the arbitrations of §7 | midas, with Orc | This proposal |
| 2 | Standard: update DIRECTIONS with the kit layout and checklist; add `template/` and the catalogue table | Sol (documentation), Revex review | 1 |
| 3 | Console demo: menu navigation (already requested) and an Overview screen | Des, Revex review | 1 |
| 4 | Console: `docs/rules/`, README screenshots and kit index, status wording, optional manifest | Des and Doc | 2, 3 |
| 5 | Console: single-file HTML build | Devex or Des, Revex review | 3 |
| 6 | Console: Docker files; Linux verified by an agent, **Windows/WSL run by a human** | Devex, then midas | 3 |
| 7 | Figma spike (§3.5), then a go/no-go | Des, with a human Figma account | 1 |
| 8 | Folio and Marquee extractions adopt the kit layout | Their owners | 2 |

Each row is one issue and one PR to `dev` in its own repository, under the usual pipeline; none is authorized by this proposal.

## 7. Arbitrations for midas

| ID | Question | Recommendation | Consequence |
| --- | --- | --- | --- |
| K1 | Which elements are required | As in §1: README, AGENTS, rules, config + example, demo with menu, checks, licences required; single-file HTML recommended; Docker and Figma optional | Light kits; optional parts never block a system |
| K2 | Figma library | Optional, after the spike; owned by Des with a named Figma account and plan | Avoids a costly manual library before feasibility is shown |
| K3 | Web preview | Screenshots + downloadable single file now; hosted preview only with H6 | No publication now |
| K4 | Generated HTML in Git | Commit it only at accepted versions, with its source commit embedded | Reviewable artefact without drift between versions |
| K5 | Framework | The standard stays framework-neutral; Console stays React | Future systems choose their implementation |
| K6 | New systems | Four-step path of §4; external repositories may be listed in the catalogue without transfer | Lower entry cost; listing is not endorsement of code outside the organization |
| K7 | Template and standard version | `template/` folder; standard declared by commit SHA until tags are authorized | No new repository or tag now |

## 8. Questions for Des (not started)

1. Which Des showcase sections and texts should become Console's Overview screen, and what is missing (motion, accessibility, configuration)?
2. What is the minimal set of rules pages per system, and their format (one page per area, or one page per component)?
3. Demo menu: labels, order, active-state treatment, and behaviour on narrow screens, using Console's own components?
4. Figma: which components and variants first; naming that matches code; can the spike be done on the available Figma plan, including two variable modes?
5. Screenshots for the README: which screens and modes best present each system?
6. For new systems, what must a proposal show for you to judge identity and coverage?

## 9. Limits

Documentation only. The Figma facts come from Figma's documentation and forum and must be confirmed in the spike; plan limits change. Docker on Windows/WSL is not verified. The single-file HTML and Docker paths are designs, not builds. No change to Console, the WakoLab frontend, repositories or permissions.
