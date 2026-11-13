# Independence check: isolated clone, external consumer and isolation

Status: **proposal for [#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1).** Revised for Revex's E1 and E3 and Des's N5–N6 ([disposition](EXTRACTION_MATRIX.md#2-disposition-of-n1n8-and-e1e4)); [previous text](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/blob/80e6f97cecabe09526a14b50e41bceeb70674bef/docs/INDEPENDENCE_CHECK.md). Each system's extraction PR runs this check in its own repository and attaches the results. It is not run by this documentation PR.

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
| Scoped CSS | Apart from `@font-face` and `@keyframes`, every selector in the emitted stylesheet starts with the system root; no rule targets `:root`, `html`, `body` or a bare `*`; cascade layers carry the system prefix |
| ESM order | Importing `ConfirmDialog` alone from the built output works (no circular-import error) |
| Shipped defaults | Contrast of the declared pairs passes in both modes; axe finds no violation on each named example; keyboard, dialog and reduced-motion checks pass |

## 3. External consumer

In a second empty directory: create a minimal Vite + React app (the same React version), install the packed tarball (or the Git dependency pinned by commit), import the stylesheet and render the documented example inside the system root.

| Check | Passes when |
| --- | --- |
| Renders alone | The example renders with no other repository present |
| Configuration | A `design-config.json` sets a few light and dark colours, a local font and a local logo, and is applied by the chosen modality (EXTRACTION_MATRIX §5.7, M1–M3) without any consumer CSS. The computed custom properties equal the values written in each mode; omitted keys keep the defaults; the font and logo load; removing a key restores its default. A malformed file or missing asset only produces a structural message. Consumer values are never judged |
| No `/api` or product call | The page makes no request other than its own assets |

## 4. Isolation (E1, N6)

One page with:
- unrelated host content outside any root (headings, paragraphs, a button, a dialog, an animated element), styled by a typical host reset;
- two system roots side by side, one light and one dark (Console twice for T1; Console with Folio once Folio exists).

| Check | Passes when |
| --- | --- |
| Host untouched | The host elements' computed styles (font, colours, margins, focus outline, animation) are identical with and without the system stylesheet loaded |
| Roots independent | Each root resolves its own tokens and mode; switching one root's mode does not change the other; the provider never writes `document.documentElement` or `body` |
| Overlays | A dialog, confirm dialog, popover and toast opened in each root use that root's tokens; each dialog's `::backdrop` equals the root's scrim; focus returns to the trigger |
| Reduced motion | With reduced motion, animations stop inside the roots and the host's animation is unaffected |
| Keyboard layer | `/`, `j` and `k` act only on the root whose collection is active; ignored in host inputs and while a dialog is open |

## 5. Visual parity

The [visual parity gate](EXTRACTION_MATRIX.md#8-visual-parity-gate-n5) passes, with the report attached.

## 6. What this check does not prove

It does not prove compatibility with every host stylesheet: host rules that target elements inside a root, or unlayered host resets, can still override the system, and they are the host's responsibility. A heuristic search for repository names is not enough on its own; the dependency and import checks above are the evidence.
