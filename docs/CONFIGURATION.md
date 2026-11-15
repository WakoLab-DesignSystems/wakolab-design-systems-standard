# Configuration specification: colours, fonts and logos

Status: **proposal; not accepted (H1 open).** This is the public, self-contained specification of the configuration file shared by every WakoLab design system (Console, Folio, Marquee and future ones). It is adapted, with provenance, from the configuration proposal integrated in the WakoLab central repository (`docs/design/design-system-standard/CONFIGURATION.md` at `be3ca126dd9a772faf0e35951a9ebac0a5a46762`); that source is optional reading and not needed to implement this file. Items marked **[proposed choice]** are Sol's recommendations awaiting review; everything else restates the accepted model.

**Accepted model (midas's decision, relayed on WakoLab#48):** the consumer sets explicit light and dark values and points to their own font and logo files. Values are used exactly as written. Nothing is derived, checked, corrected or approved. Status colours, neutral roles and the logo are configurable like any other role.

A minimal synthetic example is in [examples/design-config.example.json](examples/design-config.example.json).

## 1. File

`design-config.json`, a UTF-8 JSON object, usually kept next to the consumer's assets:

```text
design/
  design-config.json
  fonts/example-mono.woff2
  logo/mark.svg, logo/wordmark-light.svg, logo/wordmark-dark.svg
```

| Key | Type | Required | Meaning |
| --- | --- | --- | --- |
| `system` | string | yes | The system the file targets: `console`, `folio`, `marquee` |
| `systemVersion` | string | yes | The system version the file was written for. Informational: a newer system reports unknown keys structurally |
| `colors` | object | no | `light` and/or `dark`, each an object of colour roles (§2) |
| `fonts` | object | no | Font roles `ui`, `display`, `prose`, `code` (§3) |
| `logo` | object | no | Logo files and accessible name (§4) |

Each system repository ships a **`config.default.json`** listing every key with that system's default, plus a JSON schema of this file, at documented package paths (for example `config.default.json` and `config.schema.json` at the package root, both exported).

## 2. Colour roles

`colors.light` and `colors.dark` are independent: nothing is computed from one to the other.

| Role | Value | Used for |
| --- | --- | --- |
| `bg`, `surface`, `surface-2` | CSS `<color>` | Page, cards, subtle areas and hover backgrounds |
| `ink`, `muted`, `border`, `control` | CSS `<color>` | Text, secondary text, separators, control outlines |
| `action`, `action-hover`, `on-action` | CSS `<color>` | Primary actions, selected states, ghost-button text |
| `action-pressed` | CSS `<color>` | Pressed state. **When omitted, it uses the effective `action-hover` of the same mode** (the configured value, or the default) |
| `link` | CSS `<color>` | Links |
| `focus` | CSS `<color>` | Focus outline |
| `accent` | CSS `<color>` | System-specific emphasis; ignored by systems that do not use it (each system's README says so) |
| `success`, `success-soft`, `warning`, `warning-soft`, `danger`, `danger-soft`, `info`, `info-soft`, `on-danger` | CSS `<color>` | Status messages and destructive actions |
| `scrim` | CSS `<color>` | Overlay backdrop |
| `shadow-card`, `shadow-pop` | CSS `box-shadow` value | Card and overlay elevation (`none` allowed) |

**Omitted roles** keep the system's default for that mode, with one exception defined by reference: `action-pressed` (above). **[proposed choice] M4:** whether a system default written as "same as `action`" (for example Console's `link` and light `focus`) follows a configured `action` or keeps the literal default. Recommendation: follow the effective value of the referenced role in the same mode, as for `action-pressed`; consequence: changing `action` alone also changes those roles unless they are set explicitly. Until decided, implementations must document which behaviour they ship.

## 3. Fonts

```json
"fonts": {
  "ui": { "family": "Example Mono", "files": [{ "src": "fonts/example-mono.woff2", "weight": "100 800", "style": "normal" }] }
}
```

| Field | Type | Meaning |
| --- | --- | --- |
| `family` | string | The consumer's label for the font |
| `files` | array, optional | Font files, each `{ "src": Asset, "weight"?: string, "style"?: string, "stretch"?: string, "unicodeRange"?: string }`, with CSS `@font-face` descriptor syntax |

- **With `files`:** the faces are registered under an **internal family name** owned by the system and the root, never under `family`. Two roots, or a host, may use the same `family` with different files without affecting each other ([EXTRACTION_MATRIX §4](EXTRACTION_MATRIX.md#4-isolation-contract-e1-n6), R2). The `family` label is kept for documentation and diagnostics.
- **Without `files`:** `family` is used as the name of a font already available to the page (installed, or loaded by the host). It is global by nature; that is the consumer's choice.
- The system's fallback stack for the role is always appended, so scripts the font does not cover stay readable.
- Removing a role, or the whole `fonts` key, returns the role to the system default.
- The consumer is responsible for the licences of the files they add.

## 4. Logo

| Field | Type | Meaning |
| --- | --- | --- |
| `mark` | Asset | Compact logo, used on narrow screens and where only a mark fits |
| `wordmark` | Asset, or `{ "light": Asset, "dark": Asset }` | Full logo, one file for both modes or one per mode |
| `alt` | string | Accessible name of the logo link. When omitted, the shell's product label is used |

With neither `mark` nor `wordmark`, the system's text fallback is shown (Console: `›` and the product label). Sizing belongs to the shell composition, not to the configuration.

## 5. Asset paths

An Asset is a string. Importing JSON does not resolve or copy the files it names, so the rule is explicit:
- an absolute URL (`https:`, `data:`) or a root-relative path (`/…`) is used as written;
- any other path is resolved against the **`assetBase`** URL that the consumer passes together with the configuration (for example `"/design/"` when the files are served from the app's public `design/` folder). **[proposed choice]** There is no implicit default: without `assetBase`, relative paths are reported structurally and ignored;
- the consumer serves or bundles the files; the system never copies them.

## 6. Applying, changing and removing

**[proposed choice] M1:** the system's root component receives the parsed configuration and `assetBase` as props and applies them at render: colour values as custom properties on the root for the current mode, fonts per §3, logo files to the shell's logo slot. Changing or removing a key takes effect at the next render; there is no generated file to go stale. An optional build-time `apply` writer may come later; it is not part of this specification.

A consumer may also set custom properties on the root by hand. That is a documented low-level override, not a replacement for the file.

## 7. Diagnostics

Only **structural** problems are reported: invalid JSON, unknown `system`, unknown key or role, a value of the wrong JSON type, an Asset that cannot be resolved or fails to load. The key concerned is ignored and its default is used. Colour values, contrast, font choices and logos are never judged, warned about, corrected or approved; whether a string is a valid CSS colour is left to the browser.

## 8. Not configuration keys

Shape, spacing, density, layout and motion are system values. A consumer who wants other values chooses another system or edits a copy of it.
