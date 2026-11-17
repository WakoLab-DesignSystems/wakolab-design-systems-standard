# Distributable kit and contribution path: proposal

Status: **proposal by Sol for [#6](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6), revised after midas's arbitration and Orc's framing ([comment](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6#issuecomment-6930098554)), then corrected for Revex's K-R1–K-R5 ([review](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/pull/7#pullrequestreview-5929904901)).** Not implemented. It builds on the integrated standard (`dev` at `6dc2a7a`) and on Console as integrated and accepted by midas at `018fe3214e0a118162d202c2f39f1236f37b1936`. It changes nothing in Console, the WakoLab frontend or any repository setting. The first version of this proposal is at [`0237f34`](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/0237f341511b011e9905d8bfa6db745bf51605b8/docs/KIT_PROPOSAL.md).

Goal (midas): each design system is an **autonomous kit** that people and agents can understand, discover, try, customize and reuse in an external project.

**Decided and requested (midas):**
- **One self-contained HTML file** per system, combining a presentation page of the design system, the interactive examples reached through a menu, and a configuration that is kept while navigating (accepted).
- **Docker** short start, the **HTML** file and a **`.fig` companion** are requested target deliverables of every system. They may be delivered progressively; they are not optional. Figma feasibility is unproven and is handled as a risk, not as a downgrade.

## 1. Journey and minimal layout

**Journey.** Discover (README with screenshots) → try without installing (the HTML file) → try with your own colours, fonts and logo (in the HTML file, or with Docker or Node) → reuse the code in an external app → edit the library in Figma → contribute.

**Common contract versus implementation.** The common contract is framework-neutral: the configuration file and its schema, the colour/font/logo roles, the design rules (foundations, components and states, compositions, motion, accessibility), the neutral example screens, the kit deliverables and the checks on shipped defaults. A system's **implementation** is free: Console is React 19 + prebuilt CSS; a future system may use another framework, web components or CSS only, provided it ships the kit. The standard does not require React.

**Layout of a system repository** (Console paths at `018fe32` in the last column):

| Path | Use and content | Source | Status | Evidence it works | In Console? |
| --- | --- | --- | --- | --- | --- |
| `README.md` | What the kit contains, capabilities, limits, screenshots, short human start, links to everything below | Written | **Required** | Link check; steps reproduced from a clean clone | Yes; status line stale, no screenshots, no kit index |
| `AGENTS.md` | Discovery and use by an agent: where the config, rules, components, examples and deliverables are; what not to do | Written | **Required** | An agent following only `AGENTS.md` reaches the demo and applies the example configuration | Yes ("Using Console in another project") |
| `CONTRIBUTING.md`, `LICENSE`, `THIRD_PARTY_NOTICES.md` + `licenses/`, `AGENTS-SYNC.md`, `.a2a/` | Collaboration base, licences, provenance | Existing | **Required** (unchanged) | Present; notices match declared third-party material | Yes |
| `design-system.json` | Small manifest: system id and version, declared standard version, entry points, coverage, platforms | Written | **Recommended** (helps agents) | Paths exist; schema in the standard | No (README covers it in prose) |
| `config.default.json`, `config.schema.json` | Shipped defaults and the file contract ([CONFIGURATION](CONFIGURATION.md)) | Existing contract | **Required** | Schema validates defaults and example | Yes |
| `examples/config/` (Console: `examples/public/design/`) | Example configuration: light/dark colours, a font file, logo files | Synthetic | **Required** | The demo loads it; values applied exactly | Yes |
| `docs/rules/` | Design rules in short pages: foundations, components and states, compositions, motion, accessibility, identity | Des, from the system's source | **Required (short)** | Des review; linked from README and `AGENTS.md` | **No** |
| Implementation (`src/` for Console) | Components, compositions, styles | Extracted or authored | Required for an implemented system | Build, type check, tests | Yes |
| `examples/demo/` | Source of the presentation page and the interactive examples, with the menu | Real components | **Required** | Every menu entry renders; mode, settings and selected files kept across screens (§2) | Partly: `examples/neutral/` has the screens, reached by query string |
| `<system>-kit.html` | **The single self-contained HTML** (presentation + examples + configuration), generated from `examples/demo/` | Generated | **Required target** | Opens from `file://` with no network request (offline mode only, §2); same checks as the demo; byte-identical when rebuilt from its embedded source SHA (§2) | No |
| `docker/` (`Dockerfile`, `compose.yaml`) | Short start serving the same demo in its HTTP mode (§2), with configuration and assets served from an editable `design/` folder | Written | **Required target** | `docker compose up` from a clean clone; edit `design/`, reload, see the change | No |
| `design/figma/` | `.fig` companion library, plus a mapping from Figma components/variants to code | Des, with generated variables | **Required target**, subject to the feasibility spike (§3.5) | Import into Figma keeps components, variants, styles and modes editable | No |
| `docs/PROVENANCE.md`, `docs/PARITY.md` | Source mapping and parity evidence for extracted systems | Existing | Required when code is copied | Present and current | Yes |
| `tests/`, `scripts/` | Checks on shipped defaults, independence, artwork | Existing | **Required** | Run in the PR | Yes |

**Gaps to fill for Console, and only these:** `docs/rules/`; the presentation page and menu navigation; the generated HTML file; Docker; the `.fig` companion (after the spike); README screenshots, kit index, status and build wording; the optional manifest.

## 2. One HTML file, one source

| Part of the HTML file | Content | Source |
| --- | --- | --- |
| Presentation page | Identity, foundations, components and states, compositions, how to configure and reuse | A page of the demo built with the real components. Des's central showcase (`WakoLab/wakolab` `docs/design/design-system-standard/showcase/`) supplies the outline and texts, not code |
| Interactive examples | The neutral screens (collection, detail, form, message, states, components) | The existing example screens |
| Menu | Lists the presentation page and every available screen, marks the active one | Built with the system's own components |
| Configuration | Light/dark colours, fonts and logo, edited in an in-page panel or loaded from a `design-config.json` (§2, two modes) | The system's configuration contract |

**Two loading modes, one demo source.** The same demo code is built in two ways; there is no second hand-written UI.

| | Offline HTML file (`<system>-kit.html`) | HTTP demo (Docker or `npm run example`) |
| --- | --- | --- |
| Default and example resources | Embedded in the file: scripts, styles, default fonts, example configuration and example logo | Served by the server |
| Consumer configuration | Entered in the in-page panel, or a `design-config.json` chosen with a local file picker | Read from an explicit URL, `/design/design-config.json`, with `assetBase` `/design/`, at each page load and reload |
| Consumer font and logo files | Chosen one by one with a local file picker and resolved in memory. **Loading a JSON file gives no access to its neighbouring files**, so the paths it names must be matched by files the user selects | Served from the mounted `design/` folder at the URLs the configuration names; a reload reads the updated files |
| Network | **None**: the zero-network check applies to this mode only | Requests only its own configuration and assets; no product API and no undeclared external dependency |

**What navigation keeps, and what a reload keeps.**
- Moving between the presentation page and the example screens inside the same open page keeps the mode, every setting and the selected font and logo files.
- The **mode, the active screen and scalar settings** (colour values, font family names) are also written to the URL hash, so they survive a reload.
- In the offline file, **selected local files do not survive a reload**: the user selects them again. A file name, path or `blob:` URL in the hash does not give the browser access to the bytes. No storage or export mechanism is proposed here; adding one would need its own specification and evidence.
- The embedded defaults and example always remain available offline.
- In the HTTP demo, files are re-read from their URLs at each load.

**Generated file: append-only and reproducible.**
- A change that touches the demo adds **two commits** to the candidate PR: a source commit, then a commit that adds or replaces only the generated HTML file. The file embeds the **source SHA** (the source commit) and the declared standard version; the **candidate HEAD** is the later commit that contains the file. Reviewers review the file at the candidate HEAD.
- A correction during review **appends** a new source commit and then a new generated-file commit, built from that latest source SHA. Earlier commits stay as they are: no amend, rebase or force-push of published history.
- **Reproducibility check:** in a clean clone checked out at the embedded source SHA, with the locked toolchain and inputs (the repository's lockfile, the declared Node version, the build script; no timestamps or machine paths in the output), the build must produce a file **byte-identical** to the committed one (same SHA-256). Any difference blocks acceptance.
- Acceptance applies to the integrated merge; nothing is regenerated after acceptance, so there is no circular dependency between building and accepting.
- **Figma follows the code:** variables and styles are regenerated from `config.default.json`; variants follow a committed mapping table; drift is reviewed at each minor version. Figma is never the source of truth for code.

## 3. Usage paths

1. **Download and open the HTML file.** It inlines scripts, styles, default fonts and the example logo. Download it from the repository file view ("Download raw file") and double-click. Configuration is edited in the page or loaded with a local file picker; consumer font and logo files are selected locally and must be selected again after a reload (§2).
2. **Preview from the README without publishing.** GitHub shows HTML files as source, not as a running page; the README must not link them as a "live demo". The README shows committed screenshots (light, dark, configured) and links the file for download. A hosted preview (GitHub Pages or similar) is a **publication** and waits for H6. Third-party HTML preview proxies are not recommended.
3. **Docker, including Windows with WSL.**
   - A two-stage image (a Node build, then a small static server) serves the demo in its **HTTP mode** on `127.0.0.1:5190`, with `./design/` mounted and served at `/design/`. It does not serve the inlined offline file, which could not see the mounted folder.
   - Editing `design/design-config.json`, fonts or logos and reloading shows the change, because the page reads `/design/design-config.json` and its assets at each load. No rebuild or file watching is needed.
   - On Windows: Docker Desktop with the WSL 2 backend. Clone inside the WSL file system (for example `~/src`), not under `C:\`. Run `docker compose up` from a WSL terminal, then open the URL in the Windows browser.
   - **Not verified on Windows.** A human run is required before the README claims it.
4. **Use the code in an external app.** Unchanged from the integrated contract: clone and build, a local tarball, or a Git dependency pinned by commit. Import the stylesheet once, render inside the root, and pass the configuration and `assetBase`. Des's external-consumer runs already prove this for Console.
5. **`.fig` companion: feasibility.** These facts come from Figma's documentation and forum, consulted on 2026-11-16 (sources below). Figma's limits change; the spike must confirm them.
   - A `.fig` file is produced from Figma itself ("Save local copy") and can be imported back. There is no supported way to write `.fig` from code, so a human with a Figma account must make and export it.
   - Editable components, variants (component sets), text and effect styles and auto layout can be created by a **plugin** (Plugin API) or by hand.
   - Light/dark **modes** use variable collections. The number of modes per collection depends on the Figma plan, and Figma's own pages disagree on the current limits; two modes are needed.
   - The REST Variables write API is restricted to Enterprise members; the Plugin API is not. Generation should therefore use a small local plugin, or a token-import plugin, run by a human.
   - **Human interventions:** a Figma account and plan, running the plugin, drawing or adjusting components, exporting the `.fig`, and re-exporting after changes. No purchase is authorized by this proposal.
   - **Spike exit criteria:**
     - variables with light/dark modes generated from `config.default.json`;
     - colour, text and effect styles;
     - Button with its variants and states;
     - one composition with auto layout;
     - the exported `.fig`, imported into a fresh file, keeps all of these editable.

     If the spike fails on the available plan, midas decides between a plan change, a reduced companion, or deferral. The requirement is not dropped silently.
   - **Sources (consulted 2026-11-16):**
     - [Save a local copy of files](https://help.figma.com/hc/en-us/articles/8403626871063) (Figma Help): `.fig` export from the file's main menu, import by dragging into the file browser; version history and comments are not included.
     - [Modes for variables](https://help.figma.com/hc/en-us/articles/15343816063383) and [Figma plans and features](https://help.figma.com/hc/articles/360040328273) (Figma Help): the number of modes depends on the plan. The cached and current versions found that day gave different limits.
     - [Variables REST API](https://developers.figma.com/docs/rest-api/variables) (Figma developer docs): write access restricted to full members of Enterprise organizations.
     - [Forum: Variables API only on Enterprise](https://forum.figma.com/t/why-the-variables-api-is-only-available-on-enterprise-plans/45837) (Figma community): the restriction applies to the REST API, not the Plugin API.

## 4. Welcoming a new system

Three distinct things:

| Act | Who | Rights needed |
| --- | --- | --- |
| Contribute to an existing system | Anyone | None: fork and pull request to `dev`, normal review and Orc/Rel gates |
| Propose a new system | Anyone | None: an issue in the standard repository |
| Maintain a system in the organization | Named maintainers | Write access to that repository only, granted by an owner; **never admin by default** |

**Path for a system joining WakoLab-DesignSystems (four steps):**
1. **Proposal:** an issue in the standard repository. It gives the name, identity (screenshots), the source and licence of every asset, the framework and the maintainer, and says whether it is new work or an existing repository.
2. **Review and decision:** one combined review (Des: identity, coverage, neutrality; Sol: licence and provenance readiness, checklist gaps), then the maintainers' decision (midas, with Orc).
3. **Repository:** an owner creates `wakolab-design-systems-<name>` from the template (§5), or accepts a transfer of an existing repository. The proposer contributes through fork and pull request, with write access only as a named maintainer.
4. **Conformance and catalogue:** the first import PR runs the checklist in the normal pipeline. Once it is integrated and accepted, a PR adds the system to a catalogue table in the standard README.

Removed from the initial idea: a separate "dossier" step (it is the proposal issue) and a separate conformance review (it is the first PR's review).

*Separate proposed extension, not part of this path:* listing systems hosted outside the organization in the catalogue, without transfer. It needs its own decision.

## 5. Conformance checklist

| Item | Automated check | Des review | Maintainers' decision |
| --- | --- | --- | --- |
| Autonomy | No dependency on another WakoLab repository; an isolated clone builds and runs the demo | — | — |
| Documentation | README, AGENTS, CONTRIBUTING and rules pages present; links resolve | Rules are clear and complete for the components announced | — |
| Explicit configuration | Schema validates `config.default.json` and the example, including any system-specific roles; the demo applies the example exactly; omitted keys keep their own same-mode defaults (only exception: `action-pressed` → effective `action-hover`) | — | — |
| Examples and HTML file | The menu reaches every announced screen; mode, settings and selected files are kept while navigating; scalar settings survive a reload through the hash; **offline file only:** no network request; neutral texts (no WakoLab product content); the HTML file is byte-identical when rebuilt from its embedded source SHA | Presentation and examples are neutral and representative | — |
| Docker and `.fig` | `docker compose up` serves the HTTP demo from a clean clone, and an edit in `design/` appears after a reload; the `.fig` imports | The `.fig` matches the code mapping | Any deferral of a target deliverable |
| Licences and provenance | `LICENSE` is MIT; notices exist for every declared third-party package; artwork audit | — | For an asset with unknown provenance: gather the evidence, or replace or exclude the asset. **Unknown provenance is never accepted by discretion** |
| Components and rules announced | Every announced component has an example and is exported | Identity, states and coverage match the claims | Bounded exceptions (for example a parity exception) |
| Shipped defaults | Contrast and accessibility checks on the **defaults and examples only** | — | — |

The checks never evaluate a consumer's configuration values: only the system's own defaults and examples.

**Template:** a `template/` folder in this repository (copy it; do not depend on it), holding the required files with placeholders. A separate template repository is only worth it when several systems are created, and creating it is an owner action.

**Declared standard version:** each system declares the standard it follows, in its README and, when the manifest is present, in `design-system.json`. Today that is a commit SHA (Console: `6dc2a7a`); SemVer tags of the standard come later, if tagging is authorized. There is no central service or registry.

## 6. Work in coherent batches

Reviewer routing follows **the model that actually implements the work**: Codex work, including Devex and Doc when implemented by Codex, is reviewed by **Revus**; Claude work, including Des and Sol, is reviewed by **Revex**. A role label never selects the reviewer by itself; handoffs and PR summaries name the implementing model. Each batch is one issue with one or a few PRs to `dev` in its own repository, under the usual gates. No batch is authorized by this proposal.

| Batch | Content | Owner → reviewer | Depends on | Human step |
| --- | --- | --- | --- | --- |
| A. Standard | DIRECTIONS and CONFIGURATION updated (kit layout, checklist, system-specific roles of §8); `template/`; catalogue table; this proposal integrated | Sol (Claude) → Revex | midas's agreement on §7 | — |
| B. Console kit | Presentation page, menu and in-page configuration; `docs/rules/`; generated HTML file (two-commit rule, §2); README screenshots, kit index and status wording | Des (Claude) → Revex; any Doc part (Codex) → Revus | A (layout) | midas looks at the HTML file before acceptance |
| C. Console Docker | Dockerfile and compose file; Linux run recorded | Devex (Codex) → Revus | B (serves the kit) | **midas runs it on Windows/WSL** and records the result |
| D. Figma | Spike (§3.5) on Console, then the `.fig` companion and its mapping if the spike passes | Des (Claude) → Revex | B (rules and names) | A Figma account and plan; export of the `.fig` |
| E. Folio extraction | §8, adopting the kit layout | Named by Orc; reviewer by implementing model (Codex → Revus, Claude → Revex) | A; Console acquired | midas acceptance |
| F. Marquee extraction | §8, adopting the kit layout | As E | A; E's method | midas acceptance |

**Folio and Marquee extractions (E, F) do not depend on D.** Their HTML and Docker deliverables can be produced in the extraction batch or in a follow-up batch of the same shape as B and C. Their `.fig` follows D.

## 7. Arbitrations for midas

| ID | Question | Recommendation | Consequence |
| --- | --- | --- | --- |
| K1 | Layout and required elements | As in §1, with HTML, Docker and `.fig` as required targets delivered progressively | Every system converges on the same kit |
| K2 | `.fig` companion | Spike first (batch D) on the available plan; if it fails, midas chooses between a plan change, a reduced companion or deferral | No silent drop; no purchase without a decision |
| K3 | Web preview | Screenshots and a downloadable HTML file now; hosted preview only with H6 | No publication now |
| K4 | Generated HTML in Git | Append-only source and generated-file commits, byte-identical rebuild from the embedded source SHA (§2) | Reviewable before acceptance; reproducible; no circularity |
| K5 | Framework | The standard stays framework-neutral; Console stays React | Future systems choose their implementation |
| K6 | New systems | The four-step path of §4 inside the organization; external listing only as a separate later decision | A simple path now |
| K7 | Template and standard version | `template/` folder; standard declared by commit SHA until tags are authorized | No new repository or tag now |

## 8. Folio and Marquee: executable extraction scopes

Both follow the Console method. **Reuse from Console** (copied with provenance from `wakolab-design-systems-console` at `018fe32`, never imported):
- package layout, build and `prepare` scripts;
- root isolation, with the global-symbol prefix `wds-folio-` or `wds-marquee-`;
- configuration loader and diagnostics, and font registration;
- the check scripts (closure, artwork, defaults contrast), the external-consumer and parity tooling, and the test structure;
- `config.default.json` / schema shape, README and AGENTS structure, and the kit layout of §1.

The **source** stays `WakoLab/wakolab-front` at `e492bec`, read-only: no change, cleanup or pin in the frontend. **Icons stay excluded** (same E4 rule).

**Common acceptance**, as for Console:
- isolated clone and external consumer;
- isolation tests;
- shipped-defaults contrast;
- visual parity against frontend baselines at 360, 768, 1024 and 1440 px, light and dark, for each system's own screens, with no general tolerance;
- configuration applied exactly;
- the kit elements (HTML and Docker in the extraction or a follow-up batch; `.fig` after D).

| | Folio | Marquee |
| --- | --- | --- |
| Identity to preserve | Editorial: grouped index under group titles, sticky filters, article with a sticky side card, figure only when a cover exists, centered form column with serif titles; DM Sans UI, **Source Serif 4** display (600, tracking −0.01em); radius 8/12/pill; rows 56 px minimum; subtle card shadow in light mode only; 150 ms motion | Bold: chips, shelves of three tiles with "See all", tiles with cover or initials and a **3 px hover lift at 180 ms** (removed with reduced motion), a 21:9 bounded banner, section cards, a raised form card, large page headings; DM Sans UI, **Manrope 800** display; radius 10/16/pill; rows 56 px minimum; card shadows in both modes |
| Source (front `e492bec`) | `FolioHome.tsx`, `FolioHeader`, Folio blocks of `themes.css`, `compositions.css` and `catalog.css` (`.wl-folio*`, `.wl-entry*`, `.wl-project--folio`), `RegistrationShell` editorial aside | `MarqueeHome.tsx`, `MarqueeHeader`, Marquee blocks (`.wl-marquee*`, `.wl-tile*`, `.wl-project--marquee`, `.page-heading`) |
| System-specific adaptations | The editorial panel becomes **configurable** through three Folio roles (below), each with its shipped default in both modes; the front's shared literals stay in the front. The editorial registration aside becomes a neutral frame. Its hard-coded **`W` monogram is WakoLab branding**: replace it with a slot (default: the first letter of the product label), not the letter W. The common `accent` role keeps its own defaults (`#cef1c5` light, `#2f5c4c` dark) and has no visible consumer in Folio; it is **not** reused for the panel | `link` has its own default (not equal to `action`). The **default** `focus` value equals the **default** `ink` value in each mode (light `#19181d`, dark `#f3f1ed`); a configured `ink` does not change an omitted `focus`, which keeps its own default. `accent` is unused (documented as having no visible effect). `shadow-card` and `shadow-pop` stay **configurable** roles; their defaults (shadows in both modes) are part of the identity but not frozen. The 3 px hover lift and its 180 ms duration are system values, not configuration. Tiles and banner need the neutral `Cover` with initials |
| Shared neutral parts | `Cover` (initials), chips, item link, collection state, load-more, message and states, as in Console's list; no keyboard layer (none in the source) | Same |
| Fonts and notices | DM Sans, Source Serif 4, Noto Sans SC and Devanagari (OFL notices copied) | DM Sans, Manrope, Noto Sans SC and Devanagari (OFL notices copied) |
| Open points to settle in the batch | Des confirms the three panel role names and their uses below | None beyond the common ones |

**Folio panel roles (K-R1).** The smallest public mapping that covers the editorial panel used at front `e492bec` (`compositions.css` lines 63–68 and 107–110; `theme.css` lines 17–19 and 66–67). They are Folio-specific roles: declared in Folio's `config.default.json` and schema, set per mode like every role, applied exactly as written, and reported as unknown roles by other systems.

| Role | Used for | Default, light and dark |
| --- | --- | --- |
| `panel` | Background of the editorial panel (`data-surface="dark"`) | `#152e37` in both modes |
| `on-panel` | Text on the panel | `#edf6ee` in both modes |
| `panel-accent` | Eyebrow text and decorative border on the panel, and the focus outline inside it | `#cef1c5` in both modes |

An omitted panel role keeps its own default in each mode; it never follows `accent`, `focus` or another role. Folio's example configuration sets at least one panel role, and its checks verify the default rendering and the exact application of a configured value. No consumer value is checked or corrected. The standard's [CONFIGURATION](CONFIGURATION.md) will state, in batch A, that a system may declare such additional roles in its own defaults and schema.

## 9. Questions for Des (not started)

1. Which showcase sections and texts make Console's presentation page, and what is missing (motion, accessibility, configuration)?
2. What is the minimal set of rules pages per system, and their format (one page per area or per component)?
3. Menu: labels, order, active-state treatment and narrow-screen behaviour, using each system's own components?
4. Figma: which components and variants come first, with naming that matches the code, and can the spike run on the available plan with two variable modes?
5. Which screens and modes best present each system in README screenshots?
6. For Folio and Marquee: confirm the identity lists of §8, the Folio panel role names (`panel`, `on-panel`, `panel-accent`), the neutral monogram slot, and any screen missing from the neutral examples?
7. For new systems: what must a proposal show to judge identity and coverage?

## 10. Limits

Documentation only. The Figma facts come from Figma's documentation and forum, consulted on 2026-11-16, and must be confirmed by the spike; plan limits change. Docker on Windows/WSL is not verified. File reselection after reload is a documented limit, not a defect to solve here. The HTML file and Docker paths are designs, not builds. The Folio and Marquee identity lists come from reading the frontend source at `e492bec`; their completeness is confirmed by the extraction's parity evidence, not by this document. No change to Console, the WakoLab frontend, repositories or permissions.
