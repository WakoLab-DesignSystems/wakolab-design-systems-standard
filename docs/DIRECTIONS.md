# Directions for a WakoLab design system

Status: **proposal; not accepted (H1 open).** This is a short summary of the proposal integrated in the WakoLab central repository (`dev` at `be3ca12`, `docs/design/design-system-standard/`: CONFIGURATION, STANDARD, PROFILES, AGENT_CONTRACT). A fuller copy may follow, with provenance.

## 1. A system is autonomous

- One repository per system. It can be cloned alone and used in any external project.
- No shared runtime, package, submodule or sibling checkout. A component needed by several systems is copied into each, with its provenance.
- Third-party dependencies are declared in the system's own repository.
- The system's README links to this repository for directions; that is documentation, not a dependency.
- Each system renders only inside its own root element, with its mode on that root; its CSS, provider and overlays never affect content outside that root ([isolation contract](EXTRACTION_MATRIX.md#4-isolation-contract-e1-n6)).

## 2. What a system contains

- **Foundations:** colour roles in light and dark, typography roles (`ui`, `display`, `prose`, `code`) with fallback stacks, spacing, radius, border, elevation (`flat`, `overlay`, `scrim`), focus, density **minimums**, icon rules.
- **Components** with their states (default, hover, focus-visible, pressed, selected, disabled, busy, invalid, as applicable), keyboard and pointer behaviour, accessible names and one example per state.
- **Compositions** defined by slots, not product content: shell, collection, detail, form, states (loading, empty, error).
- **Motion** recipes (feedback, overlay, content change) with reduced-motion alternatives.
- **Defaults** that give the system its own recognisable look.

## 3. Configuration

Every system exposes the same keys:
- `colors.light` and `colors.dark`: explicit values per colour role;
- `fonts.<role>`: a family and local font files;
- `logo`: local files and an accessible name.

`system` and `systemVersion` identify the file. Every customization key is optional and is applied exactly as written; omitted keys keep the system's defaults. There is no colour derivation and no warning, rejection or approval of consumer choices. Shape, spacing, density, layout and motion are system values, not configuration keys.

## 4. Verification

The project verifies its own **shipped defaults and examples**: contrast of declared pairs, accessibility checks on examples, keyboard, reduced motion and reflow. It does not validate consumer choices. Each system also passes the [independence check](INDEPENDENCE_CHECK.md).

## 5. Licence and provenance

MIT for original work. Third-party fonts, icons and other assets keep their own licences and notices. Copied code keeps a note of its source.
