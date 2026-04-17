---
name: ds-figma-to-react-tw
description: "Use this skill whenever the user wants to convert a Figma component into production-quality React code. Triggers include: pasting a Figma link or node URL, saying 'build this component', 'convert from Figma', 'component from design', 'create a checkbox/button/input/etc.', or asking to refactor or audit an existing component that was originally exported from Figma. Also trigger when the user shares a Figma component screenshot or describes a component's variants and wants React code. If the user asks to build a component without providing a Figma link, prompt them for the Figma node URL before proceeding. Do NOT use for full pages, layouts, or multi-component app screens — only individual components and small composites."
---

# Figma → Production React + Tailwind Components

> **Language rule:** All generated files must be written in English.

Convert Figma components into accessible, token-driven, production-grade React + TypeScript + Tailwind code. One file per component. Zero custom CSS.

## Quick Reference

| Step | Action | Details |
|------|--------|---------|
| 1 | Read context | Read `CLAUDE.md` for stack, token paths — then fetch from Figma |
| 1b | Check existing | Scan `components/` — import existing components, never recreate them |
| 2 | Read tokens | Load `tailwind.config.ts` to know available utility classes |
| 3 | Classify | Identify interaction archetype |
| 4 | Design API | Create TypeScript interface extending native HTML attrs |
| 5 | Choose HTML + ARIA | Read `references/component-patterns.md` for the archetype |
| 6 | Build variants | Define `cva` variants with Tailwind utilities |
| 7 | Assemble TSX | One file, forwardRef, composable |
| 8 | Validate | Run mental checklist before delivering |
| 9 | Update STATUS.md | Mark component ✅ Done |

---

## Mode detection

Before starting, check if `components/{ComponentName}.tsx` already exists:
- **New** — file doesn't exist → create from scratch (normal flow)
- **Update** — file exists → read the current `.tsx` first, then fetch Figma, compare what changed, and propose targeted edits instead of rewriting. Add a CHANGELOG entry for what changed.

---

## Step 1: Read Project Context, Then Fetch from Figma

**First, read `CLAUDE.md`** if it exists in the project root. Extract:
- Token file paths (`tokens/tokens.css` and `tailwind.config.ts`)
- Import path for `cn()` (typically `@/lib/utils` or `../lib/utils`)
- Any project-specific notes

If CLAUDE.md is absent — ask the user before writing any code.

**Then use Figma MCP tool `get_design_context`** on the user's link. Extract:
- Component name
- Properties list (variants, booleans, content)
- Property values (e.g., Type: Primary, Secondary, Tertiary)
- Layer structure
- Applied variables/tokens
- Component description

### When in doubt — ask

If any element or behavior is unclear — stop and ask the user before writing code.

**If Figma MCP is not connected:** tell the user to connect Figma MCP before proceeding.

### Reuse existing components

Before building, scan `components/` and read `STATUS.md`. If the new component will contain elements that already exist as components in the project — **import and compose them, never recreate their code**. If an existing component's API doesn't fit the use case, report it as a gap.

**This skill produces only:** one `.tsx` file + `STATUS.md` update. Never create showcase pages, preview pages, or other HTML files.

## Step 2: Read Project Tokens

Load `tailwind.config.ts` from the project root. This tells you which utility classes are available.

Every visual value in the component must be a Tailwind utility class that resolves to a design token. Never hardcode a hex color, pixel size, or font value in className. If a value from Figma doesn't map to an existing Tailwind class, flag it — the token or config may need extending.

## Step 3: Classify the Component

Determine which **interaction archetype** this component belongs to:

```
What does the component DO?

├─ Switches between on/off?              → Toggle
├─ Picks ONE option from a set?          → Single Selection
├─ Picks MANY options from a set?        → Multi Selection
├─ User types or edits text?             → Text Entry
├─ Fires an action when clicked?         → Action Trigger
├─ Shows/hides related content?          → Disclosure
├─ Floats above the page?               → Overlay
└─ Displays information, not interactive? → Display
```

Components can combine archetypes. Read all relevant archetype sections in `references/component-patterns.md` and merge their patterns.

If the archetype is ambiguous (e.g. Figma calls it "switch" but it could be a toggle OR a segmented control), ask the user how it will be used before writing code. The wrong archetype means the wrong HTML element, which is a Critical finding in QA.

## Step 4: Design the Props API

Create the TypeScript interface. Extend native HTML attributes for the root element:

```tsx
// Buttons
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>,
  VariantProps<typeof buttonVariants> { }

// Form controls
interface CheckboxProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'type'> { }

// Display elements
interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement>,
  VariantProps<typeof badgeVariants> { }
```

**Translating Figma properties to props:**

| Figma property | React translation |
|----------------|-------------------|
| Type/Variant (multi-value) | cva `variant` prop (auto-typed by `VariantProps`) |
| Size (multi-value) | cva `size` prop (auto-typed by `VariantProps`) |
| State: Disabled | Native `disabled` attr (inherited) |
| State: Loading | Boolean prop `loading?: boolean` |
| State: Hovered, Focused, Pressed | **SKIP — Tailwind state modifiers** |
| State: Error, Success | Union or boolean prop |
| Is Checked | Native `checked` (inherited) |
| Label, Placeholder | `React.ReactNode` or `string` prop |

## Step 5: Choose HTML Element and ARIA

Read `references/component-patterns.md` and find the matching archetype. Each section contains: which HTML element to use, required ARIA attributes, keyboard interactions, and structural examples.

> **Note:** Code examples in component-patterns.md use placeholder class names. Replace them with Tailwind utilities following the patterns in Step 6.

This is non-negotiable: if a native HTML element exists for the job (`<input>`, `<button>`, `<select>`, `<dialog>`), use it.

## Step 6: Build Variants with cva + Tailwind

This is where Figma's visual design becomes code. Every visual value is a Tailwind utility class.

### When to use cva

- **2+ variants** (e.g. Button with variant × size) → use `cva`
- **0–1 variants** (e.g. Divider, simple Badge) → use `cn()` directly, skip cva

### cva structure

```tsx
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "../lib/utils";

const buttonVariants = cva(
  // Base classes — shared across all variants
  "inline-flex items-center justify-center rounded-sm text-button-2 font-medium whitespace-nowrap transition-colors disabled:opacity-40 disabled:cursor-not-allowed",
  {
    variants: {
      variant: {
        primary:    "bg-brand-primary text-primary-inverse hover:bg-brand-secondary active:bg-brand-tertiary",
        secondary:  "bg-neutral-25 text-primary hover:bg-tertiary active:bg-quaternary",
        tertiary:   "bg-transparent border border-secondary text-primary hover:border-primary active:border-secondary",
        quaternary: "bg-transparent text-primary hover:bg-tertiary active:bg-quaternary",
      },
      size: {
        sm: "p-1 text-button-2",
        md: "px-1.5 py-2 text-button-2",
        lg: "px-2 py-3 text-button-1",
      },
    },
    defaultVariants: {
      variant: "primary",
      size: "md",
    },
  }
);
```

### Focus ring

**Buttons and interactive elements (focus directly on the element):**
```
focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-focus focus-visible:ring-offset-2
```

Add to the cva base string. Every interactive component gets this.

**Form controls with hidden input (checkbox, radio, switch):**
```
peer-focus-visible:ring-2 peer-focus-visible:ring-focus peer-focus-visible:ring-offset-2
```

Applied to the visual sibling element. The hidden input has `className="peer sr-only"`.

### Interaction states pattern

| State | Tailwind modifier | Example |
|-------|------------------|---------|
| Hover | `hover:` | `hover:bg-brand-secondary` |
| Focus | `focus-visible:` | `focus-visible:ring-2` |
| Active | `active:` | `active:bg-brand-tertiary` |
| Disabled | `disabled:` | `disabled:opacity-40 disabled:cursor-not-allowed` |
| Checked | `peer-checked:` | `peer-checked:bg-brand-primary` (on sibling of hidden input) |

`disabled:pointer-events-none` is NOT needed — native `disabled` attribute already blocks events.

### Form controls — hidden input pattern

For checkbox, radio, switch — the native input is visually hidden but remains in the DOM for accessibility:

```tsx
<label className="group inline-flex items-center gap-2 cursor-pointer">
  <input
    ref={ref}
    type="checkbox"
    className="peer sr-only"
    {...rest}
  />
  {/* Visual element — all states via peer modifiers */}
  <span
    className={cn(
      "size-5 rounded-xs border-2 border-secondary",
      "flex items-center justify-center transition-colors",
      "peer-checked:bg-brand-primary peer-checked:border-brand-primary",
      "peer-focus-visible:ring-2 peer-focus-visible:ring-focus peer-focus-visible:ring-offset-2",
      "group-hover:peer-not-disabled:border-primary",
    )}
    aria-hidden="true"
  >
    {checked && <CheckIcon />}
  </span>
  {label && <span className="text-body-3 text-primary">{label}</span>}
</label>
```

Key points:
- `peer` on hidden input → `peer-checked:`, `peer-focus-visible:`, `peer-disabled:` on visual sibling
- `group` on label → `group-hover:` on visual sibling for hover state
- Icon visibility → conditional render via `checked` prop (not CSS — Tailwind `peer-checked:` can't reach into children)
- Hover/focus/active/disabled → 100% Tailwind modifiers, zero JS state

### Mapping Figma tokens to Tailwind classes

Use `tailwind.config.ts` as the lookup table:

| Figma token | CSS variable | Tailwind class |
|---|---|---|
| Colors/FG/Primary | `--colors-fg-primary` | `text-primary` |
| Colors/BG/Brand/Primary | `--colors-bg-brand-primary` | `bg-brand-primary` |
| Colors/Border/Focus | `--colors-border-focus` | `ring-focus` |
| Radius/SM | `--radius-sm` | `rounded-sm` |
| Spacing/4 | `--spacing-4` | `p-4`, `gap-4` |
| Typography/Body/2 | `--body-2-size` + line-height | `text-body-2` |
| Shadow/MD | `--shadow-md` | `shadow-md` |

If a Figma value has no matching Tailwind class → the token or config is incomplete. Flag it to the user, don't hardcode the value.

## Step 7: Assemble the TSX File

One file per component. Order inside the file:

```tsx
// 1. Header comment — component name, Figma source, date
/*
 * Component: Button
 * Figma node: 8150:765 (file: xxxxxxxxx)
 * Generated: 2026-03-31
 */

// 2. Imports
import React, { forwardRef } from "react";
import { cva, type VariantProps } from "class-variance-authority";
import { cn } from "../lib/utils";

// 3. Variant definitions (if using cva)
const buttonVariants = cva("...", { variants: { ... } });

// 4. Public types (exported)
export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  startIcon?: React.ReactNode;
  endIcon?: React.ReactNode;
  loading?: boolean;
}

// 5. Main component with forwardRef
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  function Button({ variant, size, startIcon, endIcon, loading, disabled, className, children, ...rest }, ref) {
    return (
      <button
        ref={ref}
        type="button"
        className={cn(buttonVariants({ variant, size }), className)}
        disabled={disabled || loading}
        aria-busy={loading || undefined}
        {...rest}
      >
        {startIcon && !loading && <span className="shrink-0" aria-hidden="true">{startIcon}</span>}
        <span>{children}</span>
        {endIcon && !loading && <span className="shrink-0" aria-hidden="true">{endIcon}</span>}
        {loading && <Spinner />}
      </button>
    );
  }
);

// 6. Default export
export default Button;
```

### forwardRef targets

- Form controls → ref on `<input>` / `<select>` / `<textarea>`
- Buttons → ref on `<button>`
- Display elements → ref on root `<span>` / `<div>`

Use `useId()` for stable IDs when label-input association is needed. Allow external `id` prop to override.

### className always reaches the root

Every component accepts `className` and merges it via `cn()`. This lets consumers add Tailwind utilities from outside:

```tsx
<Button className="w-full" variant="primary">Full width</Button>
```

### Floating content (dropdowns, tooltips, popovers)

When a component renders content that floats below/above the trigger (dropdown results, tooltip, popover), use `absolute` positioning with a `relative` wrapper. Add a comment warning about the overflow limitation:

```tsx
{/* NOTE: absolute positioning — may clip inside overflow:hidden containers.
    Production: consider Floating UI / Radix Popover. */}
<div className="absolute left-0 right-0 z-50 mt-md">
```

This ensures developers know the limitation exists without requiring a floating library in the design system package.

### Icon handling

Import from the project's icon library (Lucide, custom icons). Use `currentColor` for stroke/fill so icons inherit from parent's `text-*` class. Mark decorative icons with `aria-hidden="true"`.

Always pair icon sizing with stroke width. When using `w-icon-{size} h-icon-{size}`, also add `stroke-icon-{size}`. For parent selectors: `[&_svg]:w-icon-{size} [&_svg]:h-icon-{size} [&_svg]:stroke-icon-{size}`. This ensures correct proportional stroke for each icon scale (xxs=1px, xs=1.33px, s=1.5px, m=2px). For inline SVGs, set `strokeWidth` to the token value, never hardcode "2".

## Step 8: Validate Before Delivering

Run this checklist mentally. Do not print it to the user — if something fails, fix it silently.

1. **Keyboard**: Tab focuses. Space/Enter activates or toggles.
2. **Hover**: `hover:` modifier changes appearance.
3. **Focus**: `focus-visible:ring-*` shows ring. No `:focus` (avoids mouse focus ring).
4. **Disabled**: native `disabled` blocks interaction, `disabled:opacity-40`.
5. **Screen reader**: role, state, and label announced correctly.
6. **Form** (controls only): `name`, `value`, `<form>` submit work.
7. **forwardRef**: ref reaches the native element.
8. **Tokens only**: every class resolves to a design token via tailwind.config.ts. No hardcoded hex/px.
9. **No custom CSS**: zero `.css` files, zero `<style>` tags, zero `style={{}}`.
10. **className passthrough**: `className` prop reaches root via `cn()`.
11. **Icons**: `currentColor` + `aria-hidden="true"`.
12. **No guesswork**: if any behavior was assumed — ask before delivering.
13. **Stable callbacks in effects**: if a `useEffect` depends on a callback prop (`onDismiss`, `onOpenChange`, `onChange`), store it in a ref (`const cbRef = useRef(cb); cbRef.current = cb;`) and use `cbRef.current` inside the effect. This prevents the effect from re-running on every parent render when the consumer passes an inline function.

## Step 9: Update STATUS.md

After delivering the component, update `STATUS.md` in the project root:

1. If a `## Components` table exists — add or update the component row
2. If STATUS.md does not exist — skip and note it to the user

---

## Finish — TL;DR for user

After completing all steps, print a short summary:

✅ **Done:** [what was created or changed — file names, counts]
⚠️ **Gaps:** [what's missing, blocked, or needs user decision — or "none"]
➡️ **Next:** [suggested next action or skill to run]

Keep it to 3–5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.

---

## References

Read these when indicated:

- **`references/component-patterns.md`** — Read in Step 5. Organized by interaction archetypes. HTML structures, ARIA, keyboard behavior. Class names in examples are placeholders — replace with Tailwind utilities per Step 6.

- **`references/anti-patterns.md`** — Read before delivering (Step 8). Common mistakes with ❌/✅ corrections specific to React + Tailwind.
