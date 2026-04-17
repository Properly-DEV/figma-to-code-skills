---
name: ds-showcase-tw
description: 'Builds or updates a three-panel Storybook-like interactive showcase for developer handoff. Pure HTML/CSS/JS, no build step. Use when the user says: build the showcase, update showcase, add component to showcase, deploy showcase, make something like Storybook, show all variants, present the design system, interactive playground, or when components need a visual presentation layer.'
---

# ds-showcase-tw

> **Language rule:** All generated files must be written in English.

Builds a three-panel, Storybook-like interactive showcase for design system handoff.
No React, no bundler, no build step. Opens in browser directly or deploys to GitHub Pages.

**When to run:** At the end of the project, before handoff. All components should be complete. If the user asks mid-project, confirm that planned components are done first.

## Architecture

- Pure HTML + CSS + vanilla JS (no React, no bundler)
- Multi-page: `showcase/index.html` + `showcase/components/{name}.html` + `showcase/foundations/{name}.html` + `showcase/patterns/{name}.html` + `showcase/templates/{name}.html`
- File naming: **kebab-case** for all page files (e.g., `icon-button.html`, `form-control.html`, `register-form.html`)
- All showcase CSS classes prefixed with `sc-` to avoid collisions with DS classes
- DS tokens loaded only inside preview areas (never applied to showcase chrome)
- Neutral chrome design: `#111`/`#1a1a1a` sidebar, system font stack for all chrome/labels
- Layout follows `references/component-page-template.md` — agent populates data, never invents the layout structure

### Font loading

Every showcase page must `<link>` to `tokens/tokens.css` which contains `@font-face` declarations. The custom font renders inside `.sc-preview-bg` areas only. Showcase chrome uses system fonts (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`).

### React → plain HTML translation

Components are React + Tailwind/cva. The showcase renders them as plain HTML + CSS. Translation approach:

1. Read the `.tsx` file's `cva()` call and component JSX
2. Resolve each Tailwind class to its CSS value using `tailwind.config.ts` mappings and `tokens/tokens.css` variables
3. Write equivalent CSS rules using CSS custom properties (e.g., `bg-brand-primary` → `background: var(--colors-bg-brand-primary)`)
4. Create plain `<button>`, `<input>`, `<div>` elements with these styles
5. Prefix component CSS classes to avoid collision (e.g., `.btn-primary`, `.input-wrap`)
6. Check if `showcase/css/` already has component styles from a previous run — reuse them

### Fallbacks

- **No `STATUS.md`**: scan `/components/` directory and list files alphabetically
- **No `cva()` in component**: read the className logic and extract variant classes manually
- **No audit files**: show "No audit data available" in Issues tab
- **No patterns directory**: omit Patterns section from nav
- **No templates directory**: omit Templates section from nav

## Three-Panel Layout

### Left Sidebar

- **Project name** — read from `CLAUDE.md`, never hardcoded
- **Collapsible navigation tree:**
  - Foundations (Colors, Typography, Spacing, Radius, Shadows)
  - Components (grouped by category from `STATUS.md` if available, flat list otherwise)
  - Patterns (only if `/patterns/` has files)
  - Templates (only if `/templates/` has `.spec.md` files)
- **Environment Controls** (pinned below nav, `flex-shrink: 0`):
  - Theme toggle (light/dark) — only render if DS has multiple themes; hidden for single-theme systems
  - Force disabled — applies `disabled` attribute to all interactive elements AND `.is-disabled` class to wrapper divs (inputs, checkboxes, switches)
  - Force loading — triggers loading state on components that support it
  - Trigger error — applies `.error` class to input wrappers
  - Show focus rings — applies permanent `outline: 2px solid var(--colors-border-focus)` to all focusable elements via injected `<style>`
  - Responsive viewport — preset breakpoints (320 / 768 / 1024 / Full) + free drag resize handle. Targets ALL `.sc-preview-area` containers on the page
  - RTL mode — switches `dir="rtl"` on preview containers
  - Text overflow — injects long strings into button text nodes and input placeholders
  - Toggle implementation: `<button>` with `::after` pseudo-element (add `pointer-events: none` on `::after` to prevent click interception)
  - Controls only render when relevant (e.g. loading toggle only appears if the current component has a `loading` prop)

### Center Panel — Tabs

Content area must fill available height (`min-height: calc(100vh - 100px)`) to prevent layout jumping when switching between pages with different content lengths.

#### Playground tab (default)

Every component prop must be interactive — like Figma's component playground:

- **Dropdowns** for enum props (variant, size, state, type)
- **Toggle switches** for boolean props (disabled, loading, clearable, isFilled, etc.)
- **Text inputs** for string props (children text, placeholder, label text)
- **Toggle switches for slot props** (startAddon, endAddon on/off) — when a toggle enables a slot, additional nested controls appear for that slot's sub-props (e.g. addon variant, addon icon)
- **Live preview area** inside `.sc-preview-area` (resizable) → `.sc-preview-bg` (DS tokens active)
- ALL controls update the preview AND the right panel inspector in real-time

#### All Variants tab

Matrix grid of every variant × size combination. One row per variant group, cells showing each size. Include disabled state as a cell. Each cell has a label pill (`rgba(128,128,128,0.2)` background, system font).

#### Issues tab

Audit findings from `_designer/audits/{ComponentName}.audit.md`:
- Each finding with severity badge (critical / warning / info)
- If no audit file exists, show "No audit data available"

### Right Panel — Inspector (tabs)

**Visibility rule:** Show only for component pages. Hide for foundations, patterns, and templates — center panel expands to full width (`margin-right: 0`).

Implementation: `.sc-inspector.hidden { display: none }` and `.sc-main.no-inspector { margin-right: 0 }`, toggled in the page navigation JS function.

**Current selection indicator** at top: e.g., "Button · primary · md" — updates when playground controls change.

#### Design tab

Computed values for the CURRENT playground selection. This is the designer's primary verification tool — values must be specific to the exact variant+size combination selected, not generic to the component.

Grouped by:
- **Spacing & Dimensions** — height, padding, gap, icon size (all in px)
- **Colors** — background, background:hover, text, text:hover (hex values + color swatch + token name)
- **Typography** — font-family, font-size, line-height, font-weight, letter-spacing
- **Border** — border-width, border-radius, border-color (with token names)
- **States** — disabled opacity, focus ring spec, focus offset

Each row format: `property name | value (px/hex) | token name (gray, smaller)`.

When playground controls change (e.g., variant from "primary" to "secondary", size from "md" to "sm"), ALL values in this tab must update to reflect the new combination. This requires a data structure mapping each variant×size to its computed values.

#### Code tab

- Copyable JSX snippet reflecting current playground selections
- List of tokens used by this component variant
- Copy button (`navigator.clipboard.writeText()`)

#### Props tab

Full props API table: Name | Type | Default
- One row per prop from the component's TypeScript interface

## Steps

### Step 0 — Read project context

Read `CLAUDE.md` to get:
- Project name
- Theme mode (single dark, light/dark, etc.)
- Token file path
- Font information
- CSS strategy (Tailwind classes → tokens mapping)

### Step 1 — Read component list

Read `STATUS.md` to get the full list of components, their categories, and status. If `STATUS.md` doesn't exist, scan `/components/` and list all `.tsx` files.

### Step 2 — Scan source files

- `/components/` directory: list every `.tsx` file
- `/patterns/` directory (if exists): list `.html` and `.spec.md` files
- `/templates/` directory (if exists): list `.spec.md` files
- `/tokens/tokens.css`: read ALL CSS custom properties — needed for colors, spacing, typography, radius, shadows, icons

### Step 3 — Extract component data

For each component `.tsx` file, extract:
- **Variants** from `cva()` call — variant names, values, and defaults. For components without cva, read the className conditionals
- **Props** from the exported TypeScript interface — prop name, type, required/optional, JSDoc description
- **Default values** from `defaultVariants` in cva or default parameter values
- **Slot props** — any prop whose type is another component's props interface (e.g., `startAddon?: InputAddonProps`)
- **Computed design values per variant×size** — cross-reference each cva variant's Tailwind classes with token values to build a lookup table:
  ```
  { variant: "primary", size: "md" } → {
    height: "44px", padding: "8px 12px", borderRadius: "12px (--radius-md)",
    background: "#A299FF (--colors-bg-brand-primary)",
    fontSize: "18px", ...
  }
  ```
  This data powers the Design tab in the inspector.

### Step 4 — Read audit data

Read `_designer/audits/` directory (if it exists). For each component that has an `.audit.md` file, extract findings with severity levels.

### Step 5 — Generate showcase/index.html

Landing page with:
- Project header (name from CLAUDE.md)
- Overview grid: card per foundation linking to its detail page
- Overview grid: card per component linking to its detail page
- Patterns section: cards linking to pattern pages (if applicable)
- Templates section: cards linking to template pages (if applicable)

### Step 6 — Generate showcase/css/showcase.css

Read `references/component-page-template.md` — find the CSS section and generate `showcase.css` from it. This contains all `sc-` prefixed styles for the three-panel layout, controls, tabs, inspector, and utility classes.

Also include component-specific CSS (the React→HTML translations from Step 3).

### Step 7 — Generate showcase/js/showcase.js

Read `references/component-page-template.md` — find the JS section and generate `showcase.js` from it. This provides:
- Page navigation with inspector show/hide
- Tab switching
- Playground control wiring (dropdown → update preview + inspector)
- Environment control handlers (disabled, error, focus, RTL, overflow)
- Viewport resize
- Clipboard copy

### Step 8 — Generate component pages

For each component: `showcase/components/{kebab-name}.html`

Read `references/component-page-template.md` for the HTML structure, then:
1. Fill in template variables (`{{COMPONENT_NAME}}`, `{{PROPS_CONTROLS}}`, etc.)
2. Generate playground controls from extracted props data (Step 3)
3. Generate variant matrix from cva variants
4. Generate the design specs data object (variant×size → computed values)
5. Include audit issues if available (Step 4)
6. Generate Props tab table from TypeScript interface

### Step 9 — Generate foundation pages

For each foundation category: `showcase/foundations/{name}.html`

Read `references/foundation-page-template.md` for the HTML structure.

Inspector is HIDDEN on these pages. Center panel expands to full width. No tabs — just the content.

#### colors.html — ALL token categories

Parse every `--colors-*` variable from `tokens.css`. Organize into groups:

**Primitive colors** (raw palette):
- Base (white, black)
- Neutral (full scale)
- Primary (full scale)
- Secondary
- Red, Green, Orange, Blue (full scales)

**Semantic colors** (role-based):
- Foreground (`--colors-fg-*`) — primary, secondary, tertiary, quaternary, inverse, brand variants, system status colors
- Background (`--colors-bg-*`) — primary through quaternary, on-surface, inverse, brand variants, system status colors
- Border (`--colors-border-*`) — primary through quaternary, brand, focus, system status colors

Do not hardcode color lists — parse them from `tokens.css` so the showcase works for any project.

Each swatch: color preview block, name, hex value, CSS variable name.

#### typography.html — ALL type scales

Parse every typography token from `tokens.css`. Group by category:
- Display (all numbered variants)
- Heading (all numbered variants)
- Body (all numbered variants)
- Label (all numbered variants)
- Button (all numbered variants)

Each specimen: rendered sample text at actual size using the DS font + meta (name · size/line-height · weight).

#### spacing.html — ALL spacing tokens
Visual bars with px values, one row per token.

#### radius.html — ALL radius tokens
Preview boxes with applied border-radius.

#### shadows.html — ALL shadow tokens
Preview boxes with applied box-shadow.

### Step 10 — Generate pattern pages

If `/patterns/` has `.html` files: `showcase/patterns/{kebab-name}.html`

Inspector HIDDEN. Center panel full width.

Each pattern page:
- Read the pattern's `.html` file and extract ALL `<style>` content and `<body>` content
- Embed the pattern's CSS in a `<style>` tag
- Render the HTML faithfully inside `.sc-preview-area` > `.sc-preview-bg` — do NOT simplify or omit any elements
- The preview area supports resize (viewport buttons work) so responsive behavior can be tested

### Step 10b — Generate template pages

If `/templates/` has `.spec.md` files: `showcase/templates/{kebab-name}.html`

Inspector HIDDEN. Center panel full width.

Each template page has **3 tabs:**

1. **Wireframe** — gray dashed boxes showing section proportions, using real CSS grid/flex rules from the spec. Responsive — reflows at breakpoints. Resizable with breakpoint buttons (1440/1024/768/375). Section names as labels, dimension annotations.
2. **Layout Specs** — section dimensions table, spacing tables with token names, copyable CSS code blocks, breakpoint behavior.
3. **Component Map** — which patterns/components go in each section, with links. Empty slots marked "not built".

### Step 11 — Quality check

- [ ] All nav links work (href matches actual file paths)
- [ ] All playground controls update the preview in real-time
- [ ] All inspector Design tab values update when controls change
- [ ] Inspector hidden on foundation/pattern/template pages
- [ ] Environment controls work: disabled, error, focus, RTL, overflow, viewport — test on at least 2 component pages
- [ ] All pages open without a server (no fetch, no ES module imports)
- [ ] DS tokens loaded only inside `.sc-preview-bg` areas
- [ ] No hardcoded hex/px in showcase chrome
- [ ] Colors page includes ALL primitives AND semantics from tokens.css
- [ ] Typography page includes ALL type scales (no missing variants)
- [ ] Pattern pages faithfully reproduce the original HTML
- [ ] Custom font loads correctly in preview areas

## File structure

```
showcase/
  index.html                        — landing page
  css/
    showcase.css                    — all sc- prefixed styles + component CSS
  js/
    showcase.js                     — all interactive behavior
  components/
    button.html                     — kebab-case, one per component
    icon-button.html
    text-input.html
    ...
  foundations/
    colors.html
    typography.html
    spacing.html
    radius.html
    shadows.html
  patterns/
    register-form.html              — one per pattern (if any)
    ...
  templates/
    trading-page.html               — one per template (if any)
    ...
```

## Deployment

- **Primary:** GitHub Pages (Settings > Pages > Deploy from branch > main / /showcase)
- **Alternative:** Vercel (see `references/deployment.md`)
- **Local:** Just open `showcase/index.html` in a browser

For full deployment instructions, read `references/deployment.md`.

## Reference files

- `references/component-page-template.md` — HTML/CSS/JS template for component pages
- `references/foundation-page-template.md` — HTML template for foundation/token pages
- `references/deployment.md` — GitHub Pages, Vercel, and local deployment guide
- `references/react-showcase.md` — alternative React-based approach (only if user explicitly asks)

---

## Finish — TL;DR for user

After completing all steps, print:

Done: [what was created — file names, counts]
Gaps: [missing items or decisions needed — or "none"]
Next: [suggested next action]

Keep it to 3-5 lines. No jargon. The user is a designer.

Also update `_designer/session-state.md` with what was done and what to do next.
