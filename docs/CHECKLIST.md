# Conformance checklist

Status: **accepted kit policy** ([decision](https://github.com/WakoLab-DesignSystems/wakolab-design-systems-standard/issues/6#issuecomment-6933743616)); derived from [KIT_PROPOSAL §5](KIT_PROPOSAL.md#5-conformance-checklist). Each system's import or kit PR records the result of every row. A required target may be marked "not delivered yet" while it is delivered progressively; it is never marked optional.

**Rule for every check:** it concerns the system's **shipped defaults and examples** only. No check ever evaluates, warns about or corrects a consumer's configuration values.

| Item | Automated check | Des review | Maintainers' decision |
| --- | --- | --- | --- |
| Autonomy | No dependency, submodule or path to another WakoLab or WakoLab-DesignSystems repository; an isolated clone builds and runs the demo ([INDEPENDENCE_CHECK](INDEPENDENCE_CHECK.md)) | — | — |
| Documentation | README, AGENTS, CONTRIBUTING and `docs/rules/` present; links resolve; standard version declared by commit SHA | Rules are clear and complete for the announced components | — |
| Explicit configuration | `config.schema.json` validates `config.default.json` and the example, including any declared roles; the demo applies the example exactly; omitted roles keep their own same-mode defaults (only exception: `action-pressed` → effective `action-hover`) | Declared roles match real surfaces of the system | — |
| Demo and HTML file | The menu reaches the presentation page and every announced screen; mode, settings and selected files are kept while navigating; scalar settings survive a reload through the hash; **offline file only:** no network request; neutral texts; the file is byte-identical when rebuilt from its embedded source SHA | Presentation and examples are neutral and representative | — |
| Docker | `docker compose up` serves the HTTP demo from a clean clone; an edit in `design/` appears after a reload | — | Recording a human run on Windows/WSL before the README claims it |
| `.fig` companion | The `.fig` imports into a fresh Figma file with components, variants, styles and two modes editable | The `.fig` matches the code mapping | Any deferral or reduction after the spike |
| Licences and provenance | `LICENSE` is MIT; notices exist for every declared third-party package; artwork audit | — | Unknown provenance: gather the evidence, or replace or exclude the asset. **Never accepted by discretion** |
| Components and rules announced | Every announced component has an example and is exported | Identity, states and coverage match the claims | Bounded exceptions (for example a parity exception) |
| Shipped defaults | Contrast and accessibility checks on the defaults and examples | — | — |

Reviewer routing follows the implementing model: Codex work (including Devex and Doc) → Revus; Claude work (including Des and Sol) → Revex.
