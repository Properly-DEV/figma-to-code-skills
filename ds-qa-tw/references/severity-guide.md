# Severity Guide — Classifying Audit Findings

Every finding from the audit rules has a suggested severity. Use this guide to adjust when context changes the weight of a finding.

## Severity Levels

### Critical

**Definition:** The component is broken for end users or developers in ways that will cause real problems in production.

**Criteria — a finding is Critical if ANY of these are true:**

- A keyboard-only user cannot operate the component
- A screen reader cannot identify what the component is or does
- The component's props API is incompatible with standard form libraries (React Hook Form, Formik)
- There is no `forwardRef`, making imperative access impossible
- Interaction states (hover, focus) don't work because they're props instead of CSS
- The component uses a `<div>` with `onClick` instead of a native `<button>` or `<input>`
- The component crashes in SSR (bare `document`/`window` in render path)
- The component doesn't support controlled mode (ignores `value`/`checked` prop from parent)
- The component crashes or behaves incorrectly in an obvious use case

**Action:** Must be fixed before the component ships.

### Warning

**Definition:** The component works but has problems that will accumulate as technical debt, create inconsistency in the design system, or make maintenance harder.

**Criteria — a finding is Warning if ANY of these are true:**

- Hardcoded values exist where tokens should be used (colors, sizes, spacing)
- Static styles are inline where they should be CSS classes
- Naming conventions don't match the project
- The component hardcodes a width or margin, limiting where it can be used
- `className` or `style` props don't reach the root element
- SVG icons use hardcoded colors instead of `currentColor`
- Label prop accepts only `string` instead of `ReactNode`
- Rest props (`...rest`) aren't spread to the native element
- No defensive handling for undefined props, empty labels, or unexpected variant values
- Complex component uses flat props API where compound composition would be clearer

**Action:** Should be fixed before handoff. Acceptable to ship temporarily if time-boxed.

### Suggestion

**Definition:** Polish, optimization, or minor improvements that make the component cleaner but aren't blocking.

**Criteria:**

- File organization doesn't match recommended order
- `transition: all` instead of explicit properties
- Minor naming inconsistencies that don't cause confusion
- Missing `useMemo` or `useCallback` on components where performance impact is negligible
- Disabled opacity is slightly different from project convention

**Action:** Fix when convenient. Can be batched with other cleanup work.

### Systemic (cross-cutting tag, not a severity level)

A finding is Systemic when the same issue would apply to any component in the system, not just the one being audited. Systemic findings still have a regular severity (Critical, Warning, Suggestion) AND the Systemic tag. They trigger the PROMOTE flow described in audit-rules.md.

## Adjusting Severity

The suggested severity on each rule is a default. Adjust based on context:

**Upgrade to Critical:**
- A Warning-level token issue in a component that will be themed (dark mode, white-label) — hardcoded colors become Critical because they'll break theming
- A Warning-level inline-styles issue in a component that needs responsive behavior — inline styles block media queries

**Downgrade to Warning:**
- A Critical-level native-element issue in a prototype or internal tool — still wrong, but the blast radius is smaller
- A Critical-level a11y issue in a decorative component that is truly non-interactive (but verify it's really non-interactive)

**Downgrade to Suggestion:**
- A Warning-level naming inconsistency when the project has no established convention yet — there's nothing to be inconsistent with

## Report Priority Order

Within each severity level, order findings by **impact on the developer consuming the component:**

1. Things that break the public API (props changes)
2. Things that break user interaction (a11y, keyboard)
3. Things that break integration (form libraries, refs)
4. Things that break maintainability (tokens, CSS strategy)
5. Things that are polish (naming, file order)
