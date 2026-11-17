# Catalogue of WakoLab design systems

Status recorded under [#8](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/8) on 2026-11-17, from the systems' remote refs and issues. **"Accepted" refers only to what midas accepted; it does not mean the kit is complete, released or published.** No system has been released: every `main` and `staging` branch is still at its bootstrap commit. Update this page in the PR that changes a status, with the evidence link.

## Systems

| System | Repository | `dev` | Delivery status |
| --- | --- | --- | --- |
| Console | [wakolab-design-systems-console](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-console) | `018fe3214e0a118162d202c2f39f1236f37b1936` | Extraction integrated and **accepted by midas** ([record](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-console/issues/1#issuecomment-6928501589)). Kit **incomplete** (below) |
| Folio | [wakolab-design-systems-folio](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-folio) | `b249788d5947c5d9a29dea2d95f6fa3aa2c63466` | Repository bootstrapped (documentation only). **Extraction not started**; panel roles proposed in [CONFIGURATION §2.1](CONFIGURATION.md#21-system-declared-roles), awaiting Des's confirmation |
| Marquee | [wakolab-design-systems-marquee](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-marquee) | `3dfd232a0e418a034046e1d1687b5ecd6d0d2e35` | Repository bootstrapped (documentation only). **Extraction not started** |

## Console kit status (against [DIRECTIONS §2](DIRECTIONS.md#2-what-a-kit-contains))

| Element | Status at `018fe32` |
| --- | --- |
| README, AGENTS, CONTRIBUTING, LICENSE, notices, AGENTS-SYNC, A2A | Present. README status wording still says "extraction candidate" |
| `docs/rules/` | Not delivered |
| `config.default.json`, `config.schema.json`, example configuration | Present |
| Demo with menu and presentation page | Partial: neutral example screens exist and are reached by query string; the menu (requested by midas) and the presentation page are not delivered |
| Single HTML file | Not delivered |
| Docker | Not delivered |
| `.fig` companion | Not delivered; Figma spike not started (needs a named account and plan) |
| `design-system.json` (recommended) | Not present |
| Checks on shipped defaults, independence, artwork | Present |
| Standard version declared | Yes, as `6dc2a7a` in the README |
| Known limits | Icon artwork excluded (provenance unresolved); strict visual parity non-zero with a bounded corner exception; evidence from Chromium only |
