# Extraction plan: source → autonomous copy → adaptation

Status: **proposal by Sol for [#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1); no source has been extracted and no extraction is authorized.** This revision reconciles the bootstrap matrix (integrated at `80e6f97`, [previous text](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/80e6f97cecabe09526a14b50e41bceeb70674bef/docs/EXTRACTION_MATRIX.md)) with Des's findings N1–N8 ([comment](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1#issuecomment-6858609503)) and Revex's E1–E4 ([comment](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1#issuecomment-6858709196), [review](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/pull/2#pullrequestreview-5894470706)).

Goal: a **faithful, autonomous** Console extraction, then its consumption by `wakolab-front`, **without redesign**. Accepted boundaries: one autonomous repository per system; local copies with provenance; no shared package and no dependency on another WakoLab or WakoLab-DesignSystems repository; declared third-party dependencies allowed; business logic stays in the frontend.

## 1. Sources (re-verify before extracting)

| Repository | Ref | SHA |
| --- | --- | --- |
| `WakoLab/wakolab-front` | `dev`, the integrated pin in the central `dev/components.json` | `e492bec9ced8893d6940879aa8186ac75a48a272` |
| `WakoLab/wakolab` (central) | `dev`, after the integration of #49 | `be3ca126dd9a772faf0e35951a9ebac0a5a46762` |

Paths below are in `wakolab-front` at `e492bec` unless stated.

## 2. Disposition of N1–N8 and E1–E4

| ID | Finding (short) | Disposition | Where | Evidence expected in the extraction PR |
| --- | --- | --- | --- | --- |
| N1 | Identity-bearing parts missing (`CategoryChips`, `Cover`, `CatalogStatus`, `LoadMore`, `ProjectLink`, `Facts`, `MessageBlock`, back link, loading and problem states) | **Accepted.** Copied as neutral parts with props; product parts listed as staying in front | §5.3, §6 | Each neutral part has a named example; none imports catalog contracts, `useApp` or i18n |
| N2 | Console keyboard layer placed in front | **Accepted.** Generic layer copied to Console, bound to the collection's search slot and item list | §5.5 | Keyboard test on the example: `/`, `j`, `k`, ignored in inputs, with modifiers and while a dialog is open; visible hint |
| N3 | Form and states compositions have no rows | **Accepted.** Form framing and message/loading/problem states added (layout only) | §5.4 | Examples of a form page and each state, compared visually (§8) |
| N4 | No composition invariants | **Accepted.** Invariants listed per system and turned into checks | §5.4 | Visual parity (§8) plus DOM-order check on narrow widths |
| N5 | No proof that identity is preserved | **Accepted, with a correction:** `docs/evidence/directions/` does not exist at `e492bec` (only `docs/evidence/themes/*-home.png`). Baselines are captured from the frontend at `e492bec` with the same synthetic data | §8 | Side-by-side and pixel-diff report at 360, 768, 1024, 1440 px, light and dark |
| N6 | Two systems in one app override each other | **Accepted;** solved together with E1 by the root isolation contract | §4 | Multi-root isolation test (INDEPENDENCE_CHECK) |
| N7 | Folio's editorial focus (mint on the dark panel) would be lost | **Accepted for Folio:** kept as a Folio token with `panel`/`on-panel`; removed from Console and Marquee | §7 | Folio parity on the editorial panel, in the Folio task |
| N8 | Console accent glyphs `.wl-prompt`, `.wl-marker`, row `›` missing | **Accepted.** Added to the Console copy with the accent role | §5.2 | Visual parity of headings, path, tree and focused row |
| E1 | CSS, provider and overlay isolation incomplete | **Accepted.** Root isolation contract: root attribute, scoped emitted CSS, prefixed cascade layers, root-local provider, overlays inside the root | §4 | Multi-root and host-content isolation tests |
| E2 | Dialog backdrop: the effective rule is `.wl-dialog::backdrop { var(--wl-scrim) }`, not the JSX literal | **Accepted.** Keep the CSS rule and the scrim token; drop the dead JSX literal; keep the `wl-rise` animation and its reduced-motion behaviour | §5.6 | Computed `::backdrop` background equals the mode's scrim, identical to the frontend, in light and dark |
| E3 | "React-only imports" do not give a closed, styled dependency set | **Accepted.** Full closure, emitted CSS, base styles, declared dependencies and exports specified | §5.1, §9 | Isolated build, pack and external-consumer proof |
| E4 | Icon rights must be resolved before copying into public repositories | **Accepted; blocking** for the icon file. Missing evidence and an explicit alternative listed | §10 | Recorded rights evidence or an approved alternative, before the icon file is copied |

## 3. Console-first scope

**In:** Console tokens (light, dark), the Tailwind mapping and base styles it needs, all UI primitives, the neutral parts of N1, the shell, collection, detail, form and states compositions with Console's invariants, the keyboard layer, a root-local mode provider, the logo slot required by the accepted configuration model, Console's fonts and their notices, examples with synthetic data, and the checks of §8 and INDEPENDENCE_CHECK.

**Out:** Folio and Marquee (later tasks, same method), every product behaviour (§6), the configuration file and `apply` command (optional, §11), and the optional improvements of §11.

## 4. Isolation contract (E1, N6)

| Topic | Rule |
| --- | --- |
| Root | Every Console screen renders inside one element `data-wds="console"` carrying `data-mode="light"` or `"dark"`. Nothing is written to `document.documentElement` or `body` |
| Tokens | `--wl-*` and the generated `--color-*` values are declared on the root (`[data-wds="console"][data-mode="…"]`), not on `:root`. Two roots, of the same or different systems, each resolve their own values |
| Emitted CSS | The repository ships a **prebuilt** stylesheet. A build step prefixes every selector with the root: `:root`, `html` and `body` map to the root itself; `*` and the universal reduced-motion rule become `[data-wds="console"] *`; `:root:lang(…) h1` becomes `[data-wds="console"]:lang(…) h1`; every other selector becomes a descendant of the root |
| Cascade layers | Source layers (`theme`, `base`, `components`, `utilities`) are kept in their order but renamed with a system prefix (`wds-console-…`), so they do not merge with a consumer's own Tailwind layers. The source's unlayered files stay unlayered |
| Utilities | Tailwind scans only the repository's own sources. Consumers do not need Tailwind |
| Class names | `.wl-*` names are kept for fidelity; scoping prevents collisions between systems. Renaming is optional (§11) |
| Provider | `ThemeProvider` becomes a root component: it renders the root element, takes `mode` (`light`, `dark`, `system`), an optional `onModeChange` and an optional `storageKey` **without default** (no persistence unless asked). No side effect at module import. `system` follows `prefers-color-scheme` with a listener |
| Overlays | Dialog, ConfirmDialog, Popover and Toast render inside the root (the sources use no portal; keep it so). The `::backdrop` resolves the scrim from its dialog; the check verifies it in each supported browser |
| Fonts | `@font-face` rules are global by nature. They come from the declared font packages; identical family names across systems are harmless duplicates |
| Host content | Content outside the root is unaffected by the system's CSS. Host CSS that targets elements inside the root, or unlayered host resets, can still win; the host is responsible for them, and the check measures a typical reset |

## 5. Console: source → copy → adaptation

### 5.1 Tokens, foundations and base styles

| Source | Destination (Console) | Adaptation |
| --- | --- | --- |
| `design-system/themes.css`, three `[data-direction="console"]` blocks | Token stylesheet | Selector → root (§4). Remove `--wl-agent`, `--wl-agent-soft` |
| `design-system/theme.css` (`@theme inline` mapping, base `body`, `:focus-visible`, `:root` durations and fallbacks) | Foundation stylesheet | Remove `@source not "../web/prototype"`, `--color-agent*`, the Folio literals `accent`/`panel`/`on-panel` and the `[data-surface="dark"]` focus rule (N7 keeps them in Folio). `body` and `:root` rules map to the root (§4) |
| `web/src/style.css` base layer (`box-sizing`, `body` margin and line height, `h1`–`h3` typography, `overflow-wrap`, `font: inherit`, link underline offset) | Base stylesheet | Map to the root (§4). Font imports are replaced by the declared Console fonts (§9) |
| `design-system/compositions.css` reduced-motion rule (lines 208–216) and `:root:lang(zh-Hans|hi)` heading rules | Base stylesheet | Scoped to the root (§4) |
| `scripts/theme-tokens.mjs` | Repository script | Console palettes only; `agent` pairs dropped; result labelled "shipped defaults" |
| `web/public/licenses/{jetbrains-mono,dm-sans,noto-sans-sc,noto-sans-devanagari}.txt` | Third-party notices | Copied unchanged |

### 5.2 Generic CSS pieces (from `design-system/catalog.css`)

Copied into the Console stylesheet and scoped: `.wl-topbar`, `.wl-tools`, `.wl-tool*`, `.wl-tree`, `.wl-kbd`, `.wl-hint`, `.wl-state*`, `.wl-skeleton*`, `.wl-spinner`, `.wl-toast`, `.wl-dialog*`, `.wl-segmented`, `.wl-radio`, `.wl-option`, `.wl-skip`, `.wl-form*`, `.wl-badge`, `.wl-prompt`, `.wl-marker` and the row focus marker `a.wl-row:focus-visible::before` (N8), with the `wl-shimmer`, `wl-rise` and `wl-spin` keyframes and the reduced-motion rule (`catalog.css:329–333`). The `.wl-console*` blocks are copied with composition-neutral names where they carry catalog words (§5.4).

### 5.3 Components and neutral parts

| Source | Copy | Adaptation |
| --- | --- | --- |
| `web/src/components/ui/{buttons,fields,feedback,popover,search,tabs,confirm,index}.tsx` | `src/components/` | None in behaviour. Keep the `index`/`confirm` relationship as is and prove ESM import order in the built output (§9) |
| `ui/icons.tsx` | `src/components/icons.tsx` | **Blocked by E4** (§10). `agent` and `human` names stay as they are only if rights are cleared; renaming is optional |
| `parts.tsx` `Brand` | Logo slot | Text fallback unchanged (`›` + label); optional logo files (light/dark, mark/wordmark) and accessible name. Without a logo, the output is identical to the source |
| `parts.tsx` `CategoryChips`, `CategoryTree` | Collection parts | Items, selection and counts by props |
| `parts.tsx` `Cover` | `Cover` | Image or initials fallback from props |
| `parts.tsx` `CatalogStatus`, `LoadMore`, `ProjectLink` | Collection state, load-more and item-link parts | Status, labels and handlers by props |
| `project-parts.tsx` `Facts` | Key/value list | Pairs by props |
| `project-parts.tsx` `MessageBlock`, `BackToProjects`, `ProjectLoading`, `ProjectProblem` | Message block, back link, loading state, problem state | Labels, links and actions by props; no `ProjectController` |
| `features/Gallery.tsx`, `catalog/CatalogComponentsGallery.tsx` | `examples/` | Synthetic data only |

### 5.4 Compositions and invariants (N3, N4)

| Composition | Source | Console invariants that must survive |
| --- | --- | --- |
| Shell | `compositions/shells.tsx` `ConsoleHeader`, `shared.tsx` `Tools` | Sticky topbar; logo slot; search with visible `/` hint; tools in a fixed order; labels hidden below 40em with accessible names kept |
| Collection | `ConsoleHome.tsx`, `.wl-console*` CSS | 220 px sticky rail from 64rem; four-column rows with a header row from 48rem; rows with a 44 px **minimum** height; `#` headings with the accent glyph; mobile filters button |
| Detail | `compositions/projects.tsx`, `shared.tsx` `Page` | Breadcrumb prompt (`›` path); tabs; key/value sidebar; on narrow widths, DOM order title → action panel → details |
| Form | `pages.tsx` `.wl-form-page` framing; the two-column layout of `RegistrationShell` (`registration-layout`, `registration-workspace`) without content | Left-aligned, compact form column; Console framing of headings |
| States | `pages.tsx` `MessagePage`; loading and problem states | Message pages framed by the Console shell; skeletons and spinners keep their reduced-motion behaviour |

Each composition is a component with slots; groups, items and texts arrive as props. The frontend fills them (§6).

### 5.5 Keyboard layer (N2)

Source: `ConsoleHome.tsx` lines 30–45. Copy as a hook attached to the collection: `/` focuses the search slot; `j`/`k` move focus between the collection's items (selected by a composition attribute, not `#catalog-list [data-project]`); ignored in inputs, selects, text areas, with a modifier key, or while a dialog is open inside the root; a visible hint. The frontend only provides items.

### 5.6 Dialog parity (E2)

Keep `.wl-dialog::backdrop { background: var(--wl-scrim); }` (unlayered, as in the source, so it keeps winning over utilities) and the per-mode scrims (light `rgb(28 30 27 / 0.45)`, dark `rgb(0 0 0 / 0.6)`). Remove the dead `backdrop:bg-panel/60` utility from the JSX: it is overridden at `e492bec`, so the rendered backdrop does not change. Keep `.wl-dialog { animation: wl-rise … }` and the reduced-motion rules.

## 6. What stays in the WakoLab frontend

API ports and calls, the `/api` proxy, session and authentication, capabilities, catalog contracts and state (`useCatalogView`, `useProject`), routes, i18n catalogs and locales, the appearance preference key and its initial-paint logic, the `agent`/`human` identity colours, `Stage`, `Access`, `Roles`, `CodeVisibility`, `MineStatus`, `JoinPanel`, `ParticipationPanel`, `AccessNotice`, `MemberList`, `RepositoryList`, `ToolList`, `RewardsNote`, `TeamNotPublished`, registration and profile content, the prototype, product tests and the bundle budget. The frontend composes these into the system's slots (front#16).

## 7. Folio and Marquee (later tasks, same method)

Same contract as Console with their own root (`data-wds="folio"`, `"marquee"`). Specific points: Folio keeps `accent`, `panel`, `on-panel` and the panel focus rule as Folio tokens (N7); Folio invariants: index grouped under titles, sticky filters, article with sticky side card, figure only with a cover, centered form column with serif titles. Marquee invariants: chips, shelves of three tiles with "See all", tiles with cover or initials and hover lift, a 21:9 bounded banner, section cards, raised form card. For all, the narrow DOM order title → action panel → details.

## 8. Visual parity gate (N5)

1. **Baselines:** run the frontend at `e492bec` locally with synthetic data (the Playwright route mocks or prototype fixtures) and capture each Console screen: collection, detail, a form page, not-found and problem states, loading, the components gallery, and an open dialog.
2. **Candidate:** render the Console examples with the **same** synthetic texts and images.
3. Compare at 360, 768, 1024 and 1440 px, light and dark, in the same browser and fonts: pixel diff plus side-by-side images in the PR.
4. **Pass:** no difference except the listed deliberate ones. For Console the expected list is empty: the scrim is already effective and the logo slot is not used in baselines.

## 9. Build, dependencies and exports (E3)

| Item | Decision |
| --- | --- |
| Runtime | `react` and `react-dom` 19.2.0 as **peer** dependencies |
| Fonts | `@fontsource-variable/jetbrains-mono` 5.3.0, `dm-sans` 5.3.0, `noto-sans-sc` 5.3.1, `noto-sans-devanagari` 5.3.0 as dependencies, imported by the emitted stylesheet |
| Build tools | `tailwindcss` and `@tailwindcss/vite` 4.1.16, `vite` 7.3.7, `typescript` 5.9.3, a pinned selector-prefixing PostCSS plugin, as dev dependencies. Node ≥ 24, as the source |
| Outputs | `dist/index.js` (ESM, React external) with type declarations; `dist/console.css` (scoped, prebuilt) |
| Exports | `"."` → components, parts, compositions, root and keyboard hook; `"./console.css"` → stylesheet |
| Not copied | The `/api` proxy, the prototype entry, product scripts |
| Closure check | Built output imports only React and the declared font packages; every icon, utility, keyframe and base rule used by the components is present in the emitted CSS |

## 10. Icon rights (E4)

Facts: the icon paths entered the frontend through its own commits (`5cc01ff`, `53f50d0`, then `d20db27`), with no recorded origin, author statement or licence. MIT on the destination does not establish source rights.

**Missing evidence:** a recorded statement, on the Console extraction issue, that the 24 px outline paths are original project work that may be published under MIT, or the identification of their source and its licence.

**Alternative if the evidence cannot be given:** an explicitly approved replacement set under a permissive licence, chosen with Des and recorded with its notice. That is a visual change and must not be silent.

Until one of the two is recorded, `icons.tsx` is **not copied**. The other files can be prepared, but the Console extraction cannot pass its checks without icons.

## 11. Optional improvements (separate from extraction)

Not prerequisites; each needs its own task: wrapping row tagline (G13); distinct pressed and destructive-hover tokens (D4); easing values and exit motion; form error summary; neutral renaming of `Badge`, `ConnectionRow`, the `agent`/`human` icons and the `.wl-*`/`--wl-*` prefixes; DTCG tooling; the configuration file and `apply` command.

## 12. Structural choices (recommendation and consequences)

| ID | Choice | Recommendation | Consequences |
| --- | --- | --- | --- |
| S1 | Prebuilt scoped CSS, or consumer-side Tailwind scanning | **Prebuilt scoped CSS** | Consumers need no Tailwind; utilities cannot be reused by the host with Console tokens unless the host sets up its own build |
| S2 | Isolation by root attribute and selector prefix, keeping `.wl-*`/`--wl-*`; or renaming classes per system | **Root prefix, keep names** | Faithful copies and simple diffs; consumer overrides must target `[data-wds="console"][data-mode="…"]`; renaming stays optional |
| S3 | Distribution without publication | **Git dependency pinned by commit**, building `dist/` on install (`prepare`), or a copied folder | No registry or release needed (H6 untouched); installing needs the npm registry for dependencies and Node ≥ 24 |
| S4 | Icons | **Record rights evidence** (human); otherwise an approved replacement (Des, via Orc) | Blocks copying of `icons.tsx` until decided |

## 13. Executable tasks (proposal)

| Order | Task | Owner | Depends on | Acceptance |
| --- | --- | --- | --- | --- |
| T0 | Icon rights evidence or approved alternative | midas (evidence); Des via Orc only if replaced | — | Recorded on the Console extraction issue |
| T1 | Console extraction ([console#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-console/issues/1)) | Dev/Rev pair named by Orc | This plan, T0 | §4–§9 implemented with provenance headers; [INDEPENDENCE_CHECK](INDEPENDENCE_CHECK.md) passes, including multi-root isolation; visual parity (§8) passes; keyboard, dialog and reduced-motion checks pass; contrast of shipped defaults passes |
| T2 | Frontend consumption ([front#16](https://github.com/WakoLab/wakolab-front/issues/16)) | Dev/Rev pair named by Orc | T1 merged | Console pinned by commit; the frontend's Console copies removed; its product tests pass unchanged; Console screens pass the same parity gate; Folio and Marquee unchanged; the app root gives the Console root full-page height so the page background is unchanged |
| T3, T4 | Folio, then Marquee | Dev/Rev pairs | T1 method validated by T2 | Same gates with their own invariants (§7) |

Each task is one reviewed PR in one repository; rollback is reverting it, or re-pinning the previous commit in the frontend. No backlog row is pre-authorized.

## 14. Remaining blockers and decisions

- **E4 / T0:** icon rights evidence or an approved alternative (human).
- **S1–S3:** Sol's recommendations; Revex reviews them, Orc decides if a tradeoff is contested.
- **H1, H4 (owners), H5 (Web only, proposed) and H6 (publication)** stay open for midas; this plan needs none of them for T1–T2, because it uses no registry and no release.
- Browser support for the scoped `::backdrop` is verified by the T1 checks, not assumed.
