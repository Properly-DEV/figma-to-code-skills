# Stage 4 — CLAUDE.md

Generate `CLAUDE.md` in the project root. This is the "project memory" — every Claude session starts by reading this file. Keep it as lean as possible: only what a new session needs to orient itself and stay consistent.

**Component list is NOT here.** It lives in `STATUS.md`. Do not add a component status table to CLAUDE.md.

---

## Before generating — gather information

Ask the user for things you cannot infer from files:

1. **Project / client name** — e.g. "Acme Design System", "Atlas UI"
2. **Figma MCP available** — yes/no (affects how Claude should fetch data)
3. **Figma file link** — if available

Infer the rest from the generated files (tokens.css, tailwind.config.ts, icon structure).

---

## CLAUDE.md template

```markdown
# [Project name] — Design System

> Project conventions file. Read before every session.

## Stack
- React + TypeScript
- Tailwind CSS
- Tokens: `tokens/tokens.css` (values) → `tailwind.config.ts` (mapping)
- Utilities: `lib/utils.ts` (`cn()` for class merging)

## Figma
- File: [link or "unavailable"]
- MCP: [available / unavailable]
- Last export: [date]

## CSS Strategy
- Approach: Tailwind utility classes in `className`
- Config: `tailwind.config.ts` — all design tokens mapped from `tokens/tokens.css`
- Variants: use `cva` (class-variance-authority) for component variant definitions
- Class merging: use `cn()` from `lib/utils.ts` for conditional and merged classes
- Tokens: referenced as Tailwind classes — never raw hex/px values in className or style
- Interaction states: Tailwind state modifiers (`hover:`, `focus-visible:`, `active:`, `disabled:`) — NEVER JS props
- Focus ring: `focus-visible:ring-2 focus-visible:ring-focus focus-visible:ring-offset-2`
- Disabled: `disabled:opacity-40 disabled:cursor-not-allowed`
- No custom CSS classes — prefer utility composition. The only CSS file is `tokens/tokens.css`

## Decision Rules
- Before changing a file, check what other files or skills depend on it. If a change breaks something downstream — fix both or don't change.
- Never add code, props, or files that aren't needed right now. Three simple lines beat one clever abstraction.
- When a value is missing (token, prop, spec) — report the gap, don't guess. Use a placeholder with a comment and add to TODO.md.
- Before fixing a problem, ask: "what do we lose if we do nothing?" If the answer is "nothing important" — skip it.
- Focus on the 20% of issues that cause 80% of problems. Critical findings first, suggestions last.
- After every change, check: did this break something else? If yes — fix it now, not later.

## Naming Convention
- Variant values: lowercase (`"primary"`, `"secondary"`, `"tertiary"`)
- Size values: lowercase (`"sm"`, `"md"`, `"lg"`)
- Boolean props for states: `disabled` (native), `loading` (custom) — never string enums

## Component File Structure
Each component is a single `.tsx` file. Order inside the file:
1. Header comment (name, Figma node, date)
2. Imports (React, cva, cn, icon library)
3. Exported types/interfaces
4. Variant definitions with `cva`
5. Main component with `forwardRef`
6. `export default`

## Icons
- Library: [Lucide React / custom SVGs in assets/icons/ — fill based on project]
- Decorative icons: `aria-hidden="true"`
- Icon colors: `currentColor` (styled via Tailwind `text-*` class on parent or icon itself)

## Audit History
QA skill maintains `{ComponentName}.audit.md` files next to each component. Before auditing a component, read its audit file for context on previous findings and regressions. Token gaps from audits are tracked in `TODO.md`.

## Component list
See `STATUS.md` for the full list of components and their status.
```

---

## Rules

- Do not copy the template literally — fill it with actual project data
- If you don't know something — write `[to be filled]` and note it in TODO.md
- CLAUDE.md is intentionally lean — resist the urge to add more sections
- The component list, token categories, and "to do" lists all belong in STATUS.md or TODO.md, not here
- The CSS Strategy section is the most important section — downstream skills (ds-figma-to-react-tw, ds-qa-tw, ds-figma-patterns-tw) base their entire output on it
- The Decision Rules section encodes how Claude should think — keep rules concrete and testable, never abstract
