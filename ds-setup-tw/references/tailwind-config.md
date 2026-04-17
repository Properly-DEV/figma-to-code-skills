# Stage 2 — Tailwind Config

Generate three files that connect Tailwind to the design tokens from Stage 1:

1. `tailwind.config.ts` — maps CSS custom properties to Tailwind utility classes
2. `postcss.config.js` — required by Tailwind (boilerplate)
3. `lib/utils.ts` — `cn()` utility for merging class names

---

## Step 1 — Read tokens.css

Load `tokens/tokens.css` and categorize every token into one of these groups:

| Token prefix | Tailwind mapping target | Example token | Example class |
|---|---|---|---|
| `--colors-fg-*` | `textColor` | `--colors-fg-primary` | `text-primary` |
| `--colors-bg-*` | `backgroundColor` | `--colors-bg-brand-primary` | `bg-brand-primary` |
| `--colors-border-*` | `borderColor` | `--colors-border-focus` | `border-focus` |
| `--colors-neutral-*` | `colors` (shared) | `--colors-neutral-500` | `text-neutral-500`, `bg-neutral-500` |
| `--colors-brand-*` | `colors` (shared) | `--colors-brand-500` | `text-brand-500`, `bg-brand-500` |
| `--colors-red/green/orange/blue-*` | `colors` (shared) | `--colors-red-500` | `text-red-500`, `bg-red-500` |
| `--colors-base-*` | `colors` (shared) | `--colors-base-white` | `bg-base-white` |
| `--colors-component-*` | `colors` (shared) | `--colors-component-bg-badge-neutral` | `bg-component-badge-neutral` |
| `--spacing-*` | `spacing` | `--spacing-4` | `p-4`, `m-4`, `gap-4` |
| `--radius-*` | `borderRadius` | `--radius-md` | `rounded-md` |
| `--shadow-*` | `boxShadow` | `--shadow-sm` | `shadow-sm` |
| `--font-family-*` | `fontFamily` | `--font-family-body` | `font-body` |
| `--icon-size-*` | `width` + `height` | `--icon-size-s` | `w-icon-s`, `h-icon-s` |
| `--icon-stroke-*` | `strokeWidth` | `--icon-stroke-s` | `stroke-icon-s` |

Typography tokens (`--heading-*`, `--body-*`, `--button-*`, `--label-*`, `--display-*`) are mapped as custom `fontSize` entries that include line-height (see Step 2 below).

---

## Step 2 — Generate tailwind.config.ts

Both files below go in the **project root**.

### Mapping rules

**Strict mode (default):** All token categories go directly in `theme: { ... }`, NOT inside `extend`. This disables default Tailwind classes (like `bg-blue-500`) so developers can only use design system tokens. Only `width`, `height`, and `strokeWidth` stay in `extend` since icon sizes are additive. Always include `transparent`, `current`, `0`, `px`, `full`, and `none` values where needed — these Tailwind essentials are lost when overriding defaults.

**Semantic color tokens → per-utility mapping.** Strip the category prefix because Tailwind's utility already provides it:

```
--colors-fg-primary         → textColor.primary         → class: text-primary
--colors-fg-brand-primary   → textColor.brand-primary   → class: text-brand-primary
--colors-bg-primary         → backgroundColor.primary   → class: bg-primary
--colors-bg-brand-primary   → backgroundColor.brand-primary → class: bg-brand-primary
--colors-border-focus       → borderColor.focus         → class: border-focus
```

**Primitive color scales → shared `colors` object.** These work across all utilities:

```
--colors-neutral-500  → colors.neutral.500  → class: text-neutral-500, bg-neutral-500, border-neutral-500
--colors-brand-500    → colors.brand.500    → class: text-brand-500, bg-brand-500
```

**Spacing** — map as numbers, not strings:

```
--spacing-4: 16px → spacing.4 → class: p-4, m-4, gap-4
```

**Typography** — use Tailwind's `fontSize` with `[size, { lineHeight }]` tuple syntax:

```
--heading-1-size: 56px, --heading-1-line-height: 64px
→ fontSize.heading-1: ['var(--heading-1-size)', { lineHeight: 'var(--heading-1-line-height)' }]
→ class: text-heading-1
```

### Full example

```ts
import type { Config } from "tailwindcss";

const config: Config = {
  content: [
    "./components/**/*.{ts,tsx}",
    "./patterns/**/*.{ts,tsx,html}",
    "./lib/**/*.{ts,tsx}",
  ],
  // darkMode — set based on theme mode from Stage 1:
  // Dark only or Light only → omit this line entirely
  // Both with .dark class    → darkMode: "class",
  // Both with data-theme     → darkMode: ["selector", "[data-theme='dark']"],
  theme: {
    // ── STRICT MODE ──────────────────────────────────────
    // All token categories are defined OUTSIDE extend.
    // This means ONLY design system tokens are available as Tailwind classes.
    // Default Tailwind colors (bg-blue-500 etc.) are disabled.
    // Only width, height, strokeWidth stay in extend (additive).

    // ── Primitive color scales ────────────────────────────
    colors: {
      transparent: "transparent",
      current: "currentColor",
      base: {
        white: "var(--colors-base-white)",
        black: "var(--colors-base-black)",
      },
      neutral: {
        25:  "var(--colors-neutral-25)",
        50:  "var(--colors-neutral-50)",
        100: "var(--colors-neutral-100)",
        // ... remaining scale from tokens.css
      },
      brand: {
        50:  "var(--colors-brand-50)",
        // ... remaining scale from tokens.css
      },
      red:    { 500: "var(--colors-red-500)" },
      green:  { 500: "var(--colors-green-500)" },
      orange: { 500: "var(--colors-orange-500)" },
      blue:   { 500: "var(--colors-blue-500)" },
    },

    // ── Semantic: text colors ────────────────────────────
    textColor: {
      primary:         "var(--colors-fg-primary)",
      secondary:       "var(--colors-fg-secondary)",
      tertiary:        "var(--colors-fg-tertiary)",
      quaternary:      "var(--colors-fg-quaternary)",
      "primary-inverse": "var(--colors-fg-primary-inverse)",
      "brand-primary":   "var(--colors-fg-brand-primary)",
      "brand-secondary": "var(--colors-fg-brand-secondary)",
      "brand-tertiary":  "var(--colors-fg-brand-tertiary)",
      "system-error":    "var(--colors-fg-system-error-primary)",
      "system-success":  "var(--colors-fg-system-success-primary)",
      "system-warning":  "var(--colors-fg-system-warning-primary)",
      "system-info":     "var(--colors-fg-system-info-primary)",
    },

    // ── Semantic: background colors ──────────────────────
    backgroundColor: {
      primary:    "var(--colors-bg-primary)",
      tertiary:   "var(--colors-bg-tertiary)",
      quaternary: "var(--colors-bg-quaternary)",
      "brand-primary":   "var(--colors-bg-brand-primary)",
      "brand-secondary": "var(--colors-bg-brand-secondary)",
      "brand-tertiary":  "var(--colors-bg-brand-tertiary)",
      "system-success": "var(--colors-bg-system-success-primary)",
      "system-warning": "var(--colors-bg-system-warning-primary)",
      "system-error":   "var(--colors-bg-system-error-primary)",
      "system-info":    "var(--colors-bg-system-info-primary)",
      "component-badge-neutral": "var(--colors-component-bg-badge-neutral)",
      "component-dialog":        "var(--colors-component-bg-dialog)",
    },

    // ── Semantic: border colors ──────────────────────────
    borderColor: {
      primary:   "var(--colors-border-primary)",
      secondary: "var(--colors-border-secondary)",
      tertiary:  "var(--colors-border-tertiary)",
      focus:     "var(--colors-border-focus)",
      "brand-primary":   "var(--colors-border-brand-primary)",
      "brand-secondary": "var(--colors-border-brand-secondary)",
      "system-success": "var(--colors-border-system-success-primary)",
      "system-warning": "var(--colors-border-system-warning-primary)",
      "system-error":   "var(--colors-border-system-error-primary)",
      "system-info":    "var(--colors-border-system-info-primary)",
    },

    // ── Ring color (focus rings) ─────────────────────────
    ringColor: {
      focus: "var(--colors-border-focus)",
    },

    // ── Spacing ──────────────────────────────────────────
    spacing: {
      0: "0",
      px: "1px",
      full: "100%",
      4:  "var(--spacing-4)",
      5:  "var(--spacing-5)",
      6:  "var(--spacing-6)",
      8:  "var(--spacing-8)",
      10: "var(--spacing-10)",
    },

    // ── Border radius ────────────────────────────────────
    borderRadius: {
      none: "0",
      xs:   "var(--radius-xs)",
      sm:   "var(--radius-sm)",
      md:   "var(--radius-md)",
      xl:   "var(--radius-xl)",
      full: "var(--radius-full)",
    },

    // ── Box shadow ───────────────────────────────────────
    boxShadow: {
      none: "none",
      sm: "var(--shadow-sm)",
      md: "var(--shadow-md)",
      lg: "var(--shadow-lg)",
    },

    // ── Font family ──────────────────────────────────────
    fontFamily: {
      headings: "var(--font-family-headings)",
      body:     "var(--font-family-body)",
    },

    // ── Font size (with line-height) ─────────────────────
    fontSize: {
      "display-1": ["var(--display-1-size)", { lineHeight: "var(--display-1-line-height)" }],
      "display-2": ["var(--display-2-size)", { lineHeight: "var(--display-2-line-height)" }],
      "heading-1": ["var(--heading-1-size)", { lineHeight: "var(--heading-1-line-height)" }],
      "heading-2": ["var(--heading-2-size)", { lineHeight: "var(--heading-2-line-height)" }],
      "heading-3": ["var(--heading-3-size)", { lineHeight: "var(--heading-3-line-height)" }],
      "heading-4": ["var(--heading-4-size)", { lineHeight: "var(--heading-4-line-height)" }],
      "heading-5": ["var(--heading-5-size)", { lineHeight: "var(--heading-5-line-height)" }],
      "body-1": ["var(--body-1-size)", { lineHeight: "var(--body-1-line-height)" }],
      "body-2": ["var(--body-2-size)", { lineHeight: "var(--body-2-line-height)" }],
      "body-3": ["var(--body-3-size)", { lineHeight: "var(--body-3-line-height)" }],
      "body-4": ["var(--body-4-size)", { lineHeight: "var(--body-4-line-height)" }],
      "label-6": ["var(--label-6-size)", { lineHeight: "var(--label-6-line-height)" }],
      "button-1": ["var(--button-1-size)", { lineHeight: "var(--button-1-line-height)" }],
      "button-2": ["var(--button-2-size)", { lineHeight: "var(--button-2-line-height)" }],
    },

    // ── Extensions (additive utilities only) ─────────────
    extend: {
      // Icon sizes and strokes ADD to defaults, not replace
    },
  },
  plugins: [],
};

export default config;
```

### Generating from tokens.css (not copy-paste)

The example above matches the current RLY Starter Kit tokens. For a new project, **read the actual tokens.css** and generate the config dynamically:

1. Parse each `--token-name: value` line
2. Group by prefix
3. Semantic tokens (`fg`, `bg`, `border`) → per-utility mapping
4. Primitive scales (`neutral`, `brand`, system colors) → `colors` object
5. Spacing, radius, shadow, font-family → direct mapping
6. Typography groups (heading, body, button, label, display) → `fontSize` tuples

**Do not hardcode values from the example above.** Read the project's tokens.css and generate accordingly. The example is a reference for structure, not for copying.

---

## Step 3 — Generate postcss.config.js

This is boilerplate — same for every project:

```js
// postcss.config.js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
};
```

---

## Step 4 — Generate lib/utils.ts

```ts
// lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

This function is used by every component to merge class names safely. It does two things:
- `clsx` handles conditional class strings (falsy values are dropped)
- `twMerge` resolves Tailwind class conflicts (e.g. `px-4` + `px-2` → only `px-2` survives)

---

## Step 5 — Remind about dependencies

After generating all three files, remind the user:

> "These npm packages are required. If you haven't installed them yet:"
> ```
> npm install tailwindcss postcss autoprefixer class-variance-authority clsx tailwind-merge
> ```

`class-variance-authority` is not used in this stage, but will be needed by every component. Better to install now.

---

## Verification after Stage 2

- [ ] `tailwind.config.ts` exists in root
- [ ] Every token from tokens.css is mapped in the config
- [ ] Semantic tokens use per-utility mapping (no `bg-bg-*` double prefixes)
- [ ] `content` paths cover `components/`, `patterns/`, `lib/`
- [ ] `darkMode` strategy matches the dark mode block in tokens.css (if present)
- [ ] `postcss.config.js` exists in root
- [ ] `lib/utils.ts` exists and exports `cn`
- [ ] User has been reminded about required dependencies

Show the user a summary of what was generated and ask to confirm before proceeding to Stage 3.
