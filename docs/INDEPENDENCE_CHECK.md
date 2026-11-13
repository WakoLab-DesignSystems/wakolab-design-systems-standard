# Independence check: use from an isolated clone

Status: **proposal for [#1](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/1).** Each system's acceptance includes this check, run in its own repository. It proves that the system works when no other WakoLab or WakoLab-DesignSystems repository is present.

## Setup

- A fresh, empty working directory, for example a new temporary directory or a clean container.
- No sibling checkout of any WakoLab or WakoLab-DesignSystems repository, and no global package link pointing to one.
- Network access limited to the public package registry and the system's own repository.

## Steps

```bash
git clone https://github.com/WakoLab-DesignSystems/wakolab-design-systems-console system
```

```bash
cd system && npm ci && npm run build && npm test
```

Then the external-consumer example:

1. Pack the system: `npm pack` in the clone.
2. In a second empty directory, create a minimal Vite + React app and install the packed tarball (or copy the system's folder into the app, if the system documents that path).
3. Render the documented example screen, then add a `design-config.json` that overrides a few light and dark colours and points to a local font and logo.
4. Check in a browser (for example with Playwright): the overridden custom properties equal the values written, in each mode; omitted keys keep the defaults; the logo and font files load; the shipped defaults pass the system's own accessibility checks.

## What must be true

| Check | How |
| --- | --- |
| No inter-repository dependency | No `.gitmodules`; `package.json` and the lockfile contain no `WakoLab` or `WakoLab-DesignSystems` Git URL, `file:../` path or workspace reference |
| No hidden path | `grep -rn "\.\./wakolab\|wakolab-design-systems-" --include=*.{js,ts,tsx,css,json,mjs}` finds nothing outside provenance comments and documentation links |
| Builds and tests alone | The steps above succeed with nothing else present |
| Works in an external app | The consumer example renders and applies the configuration exactly |
| Third-party dependencies declared | Every package the build uses is listed in the repository's own `package.json` |

Links to the standard repository in README files are documentation and are allowed.

## Bootstrap state

At bootstrap (before any extraction), each repository contains only documentation and an A2A log. The check then reduces to: no `.gitmodules`, no package manifest, and no reference to another repository other than documentation links. The result of that reduced check is reported on the bootstrap pull requests; it does not replace the full check after extraction.
