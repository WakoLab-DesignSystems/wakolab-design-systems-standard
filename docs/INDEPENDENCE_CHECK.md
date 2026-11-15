# Independence check: isolated clone, external consumer and isolation

Status: **proposal for [#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1).** Revised for Revex's E1 and E3 and Des's N5–N6 ([disposition](EXTRACTION_MATRIX.md#2-disposition-of-n1n8-and-e1e4)), then for Revex's R1–R4 ([disposition](EXTRACTION_MATRIX.md#disposition-of-r1r4-and-related-observations)); [previous text](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/80e6f97cecabe09526a14b50e41bceeb70674bef/docs/INDEPENDENCE_CHECK.md). Each system's extraction PR runs this check in its own repository and attaches the results. It is not run by this documentation PR.

## 1. Setup

- A fresh, empty directory or clean container.
- No checkout of any other WakoLab or WakoLab-DesignSystems repository, and no global package link to one.
- Network limited to the public npm registry and the system's own repository.
- One pinned browser version (Chromium through Playwright at minimum; Firefox and WebKit for the `::backdrop` and root-scoping cases if available, otherwise recorded as a limit).

## 2. Isolated build

```bash
git clone https://github.com/WakoLab-DesignSystems/wakolab-design-systems-console system
```

```bash
cd system && npm ci && npm run build && npm test && npm pack
```

| Check | Passes when |
| --- | --- |
| No inter-repository dependency | No `.gitmodules`. `package.json` and the lockfile contain no Git URL, `file:` path or workspace reference to a WakoLab or WakoLab-DesignSystems repository. The package's **own** name and README documentation links are allowed |
| Declared dependencies | Every import in `dist/index.js` is `react`, `react-dom`, `react/jsx-runtime` or the package itself; every `@import` in the stylesheet is a declared font package |
| Closed styling | Every class used by the components and examples is present in the emitted stylesheet (utilities, `.wl-*` pieces, keyframes, base rules) |
| Scoped CSS | Every selector in the emitted stylesheet starts with the system root; no rule targets `:root`, `html`, `body` or a bare `*`; cascade layers carry the system prefix |
| Global symbols (R1) | An audit of the emitted stylesheet lists every global name: `@keyframes`, `@property`, `@counter-style`, `@font-palette-values`, `@position-try`, `view-transition-name`, `@font-face` families. Each carries the `wds-<system>-` prefix in its definition and in every reference (`animation`, `animation-name`, `var()`, `font-family`), or is listed with its reason |
| ESM order | Importing `ConfirmDialog` alone from the built output works (no circular-import error) |
| Shipped defaults | Contrast of the declared pairs passes in both modes; axe finds no violation on each named example; keyboard, dialog and reduced-motion checks pass |

## 3. External consumer

In a second empty directory: create a minimal Vite + React app (the same React version), install the packed tarball (or the Git dependency pinned by commit), import the stylesheet and render the documented example inside the system root.

| Check | Passes when |
| --- | --- |
| Renders alone | The example renders with no other repository present |
| Configuration | The [example file](examples/design-config.example.json), adapted to the app's asset folder, is passed with an `assetBase` and applied by the chosen modality (EXTRACTION_MATRIX §5.7, M1–M3) without any consumer CSS. The computed custom properties equal the values written in each mode; omitted keys keep the defaults; an omitted `action-pressed` equals the effective `action-hover` of the same mode; the font and both logo forms load from the consumer's files; removing a key restores its default at the next render; without a logo the text fallback renders. A malformed file or missing asset only produces a structural message. Consumer values are never judged |
| Action only (C1) | A file sets only `colors.light.action`, with `link` and `focus` omitted: light `action` takes the value; light `link` and `focus` keep their shipped defaults; light `action-pressed` follows the effective light `action-hover` (the default here); the dark mode is unchanged. Then `link` and `focus` are set explicitly and take their values; removing them restores the defaults; setting `colors.dark.action` alone changes only the dark mode |
| No `/api` or product call | The page makes no request other than its own assets |

## 4. Isolation (E1, N6)

One page with:
- unrelated host content outside any root (headings, paragraphs, a button, a dialog, an animated element), styled by a typical host reset;
- two system roots side by side, one light and one dark (Console twice for T1; Console with Folio once Folio exists).

| Check | Passes when |
| --- | --- |
| Host untouched | The host elements' computed styles (font, colours, margins, focus outline, animation) are identical with and without the system stylesheet loaded |
| Global-symbol collisions (R1) | The host stylesheet and a second "foreign system" fixture stylesheet define the source's unprefixed names (`wl-rise`, `wl-spin`, `wl-shimmer`, `spin`) with different keyframes, and register `@property --tw-*` names with different syntax or initial values. Loaded **before and after** the system stylesheet, in both orders: host and fixture animations keep their own keyframes and values; the system's animations and computed values are unchanged |
| Fonts (R2) | (a) Two roots and the host each use the same `family` label (for example `Example Sans`) with **different** files: each renders its own file (checked through `document.fonts` and rendered glyph metrics). (b) One role with several files (regular 400, bold 700, italic, and a script subset with `unicodeRange`): all are registered under one internal family; bold text uses the bold file, not synthetic bold; italic and subset text use their files. (c) Two roots with the same face set share one family and its registrations. (d) One root replaces or removes one face: it switches to the new family; the other root keeps rendering its faces, and none of its registrations is deleted. (e) Removing the role restores the default; unmounting both roots deletes every registration, and none remains in `document.fonts`. (f) Default fonts are unaffected by a host face that reuses the public family name |
| Roots independent | Each root resolves its own tokens and mode; switching one root's mode does not change the other; the provider never writes `document.documentElement` or `body` |
| Overlays | A dialog, confirm dialog, popover and toast opened in each root use that root's tokens; each dialog's `::backdrop` equals the root's scrim; focus returns to the trigger |
| Overlay events and focus (R3) | With a popover open in root A: Escape typed in host content or in root B does not close it and moves no focus; Escape inside it closes it and returns focus to its trigger; an outside pointer-down closes it without moving focus. With popovers open in both roots, each reacts only to its own focus. With a nested popover, Escape closes the innermost first. Listeners are removed after close and unmount (no handler left on `document`). A native modal dialog makes host content and the other root inert while open, as intended; the keyboard layer and popover shortcuts of every root stay suppressed until it closes |
| Reduced motion | With reduced motion, animations stop inside the roots and the host's animation is unaffected |
| Keyboard layer | With the default root-scoped listener, `/`, `j` and `k` act only on the root that holds focus; ignored in host inputs, editable content and while any modal dialog is open. With the page-level option on a single root, `/` also works when nothing is focused |
| Nested roots | Not tested: excluding nested system roots is a proposed v1 boundary (EXTRACTION_MATRIX §4), pending a decision |

## 5. Visual parity

The [visual parity gate](EXTRACTION_MATRIX.md#8-visual-parity-gate-n5) passes, with the report attached.

## 6. What this check does not prove

It does not prove compatibility with every host stylesheet: host rules that target elements inside a root, or unlayered host resets, can still override the system, and they are the host's responsibility. A heuristic search for repository names is not enough on its own; the dependency and import checks above are the evidence.
