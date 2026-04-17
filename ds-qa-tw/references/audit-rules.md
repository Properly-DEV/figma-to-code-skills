# Audit Rules — Five Dimensions

Every component is evaluated across five dimensions.

## Table of Contents

1. [Props API](#1-props-api)
2. [Styles & Tokens](#2-styles--tokens)
3. [Interaction States](#3-interaction-states)
4. [Accessibility](#4-accessibility)
5. [Architecture & Scalability](#5-architecture--scalability)

---

## 1. Props API

### Rule 1.1 — Extend native HTML attributes [Critical]

The props interface must extend the native HTML attributes of its root interactive element.

```tsx
// ❌ Custom interface with no native attrs
interface ButtonProps { label: string; onClick?: () => void; disabled?: boolean; }

// ✅ Extends native — gets onClick, disabled, type, form, aria-*, data-*, etc.
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement>,
  VariantProps<typeof buttonVariants> {}
```

**Which element to extend:**

| Component type | Extend | Root element |
|---------------|--------|--------------|
| Button, IconButton | `ButtonHTMLAttributes<HTMLButtonElement>` | `<button>` |
| Text input | `InputHTMLAttributes<HTMLInputElement>` | `<input>` |
| Textarea | `TextareaHTMLAttributes<HTMLTextAreaElement>` | `<textarea>` |
| Checkbox, Radio, Switch | `Omit<InputHTMLAttributes<HTMLInputElement>, 'type'>` | `<input>` |
| Link | `AnchorHTMLAttributes<HTMLAnchorElement>` | `<a>` |
| Select | `SelectHTMLAttributes<HTMLSelectElement>` | `<select>` |
| Display (badge, tag) | `HTMLAttributes<HTMLSpanElement>` | `<span>` |
| Container (card, panel) | `HTMLAttributes<HTMLDivElement>` | `<div>` |

### Rule 1.2 — No state prop for interaction states [Critical]

Hover, focus, active/pressed are browser events. They must never be React props.

```tsx
// ❌ Figma variants leaked into code
interface ButtonProps { state: "Default" | "Hovered" | "Focused" | "Pressed" | "Disabled"; }

// ✅ Disabled comes from native attr, rest from Tailwind modifiers
interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> { }
```

**Exception:** `loading` is valid. Error/success statuses on form fields are valid — they depend on validation logic, not browser events.

### Rule 1.3 — Boolean states over string enums [Warning]

```tsx
// ❌ Figma naming
isChecked: "No" | "Yes" | "Indeterminate"

// ✅ Standard React types
checked?: boolean
indeterminate?: boolean
```

### Rule 1.4 — Standard onChange + controlled/uncontrolled [Critical]

```tsx
// ❌ Custom callback — breaks React Hook Form
onChange?: (value: string) => void

// ✅ Standard React event
onChange?: React.ChangeEventHandler<HTMLInputElement>
```

### Rule 1.5 — Labels accept ReactNode [Warning]

`label?: string` → `label?: React.ReactNode`.

### Rule 1.6 — forwardRef on the correct element [Critical]

Every component must use `forwardRef` targeting the primary native element.

### Rule 1.7 — Rest props spread + className reaches root via cn() [Warning]

```tsx
// ❌ className lost or concatenated unsafely
function Button({ variant, className }: ButtonProps) {
  return <button className={`bg-brand-primary ${className}`}>...</button>;
}

// ✅ className merged via cn() — resolves Tailwind conflicts
function Button({ variant, size, className, ...rest }: ButtonProps) {
  return <button className={cn(buttonVariants({ variant, size }), className)} {...rest}>...</button>;
}
```

### Rule 1.8 — API ergonomics and naming [Warning]

- **Sensible defaults:** `<Button />` with zero props renders a working button.
- **Intuitive names:** `variant`, `size`, `disabled` — not `visualStyle`, `magnitude`.
- **Minimal required props:** 5+ required = too demanding.
- **Consistent naming:** variant values match project convention (lowercase `"primary"` or PascalCase `"Primary"` — whatever CLAUDE.md declares).

---

## 2. Styles & Tokens

### Rule 2.1 — No arbitrary values [Warning]

Tailwind's `[#hex]`, `[14px]`, `[8px]` syntax bypasses the design token system. Every visual value must resolve to a named Tailwind class mapped in `tailwind.config.ts`.

```tsx
// ❌ Arbitrary values — bypasses tokens
className="bg-[#00ec88] text-[14px] rounded-[8px] p-[6px]"

// ✅ Named token classes
className="bg-brand-primary text-body-3 rounded-sm px-1.5"
```

**Exception:** One-off structural values with no system-level equivalent (e.g. `w-[2px]` for a divider stroke, `translate-y-[1px]` for optical alignment). These must be rare and commented.

If a Figma value has no matching class → flag the token or config as incomplete, don't hardcode.

This also applies to JS props: if a component accepts `size={20}` but a token class like `w-icon-s` exists — use the class. Hardcoded values in props bypass the token system just like `[20px]` in className.

### Rule 2.2 — No inline styles for static values [Warning]

```tsx
// ❌ Static values in style={{}}
<div style={{ borderRadius: "8px", padding: "0 6px", color: "#595959" }}>

// ✅ Tailwind utilities
<div className="rounded-sm px-1.5 text-secondary">
```

`style={{}}` is acceptable only for truly dynamic values (calculated positions, progress percentages, values from props that can't be expressed as Tailwind classes).

### Rule 2.3 — No custom CSS alongside Tailwind [Warning]

```tsx
// ❌ Custom CSS class mixed with Tailwind
// somewhere.css: .button-primary { background-color: var(--colors-bg-brand-primary); }
className="button-primary rounded-sm px-2"

// ✅ Pure Tailwind utilities
className="bg-brand-primary rounded-sm px-2"
```

One styling system, no exceptions. Custom CSS classes can't be resolved by `tailwind-merge` inside `cn()`, creating unpredictable override behavior.

### Rule 2.4 — No transition-all [Suggestion]

```tsx
// ❌ Animates layout properties
className="transition-all duration-150"

// ✅ Explicit
className="transition-colors duration-150"
```

### Rule 2.5 — SVG icons use currentColor [Warning]

`stroke="currentColor"` / `fill="currentColor"`. Color controlled by parent's `text-*` class.

### Rule 2.6 — cva used appropriately [Warning]

- **2+ variant dimensions** → `cva` required. Without it, variant logic becomes string concatenation spaghetti.
- **0–1 variant dimensions** → `cn()` directly. `cva` import adds weight for zero benefit.

```tsx
// ❌ cva for a zero-variant component
const dividerVariants = cva("h-px bg-tertiary");

// ✅ cn() directly
<div className={cn("h-px bg-tertiary", className)} />
```

```tsx
// ❌ Manual string ternaries for multi-variant component
className={`${variant === "primary" ? "bg-brand-primary" : "bg-transparent"} ${size === "sm" ? "p-1" : "p-2"}`}

// ✅ cva with typed variants
const buttonVariants = cva("inline-flex items-center ...", {
  variants: { variant: { primary: "bg-brand-primary", tertiary: "bg-transparent" }, size: { sm: "p-1", md: "p-2" } }
});
```


### Rule 2.7 — Semantic tokens over primitive Tailwind colors [Warning]

Standard Tailwind color classes (`bg-blue-500`, `text-red-600`) map to primitive palette values. When the project defines semantic CSS variables for the same intent, the semantic token must be used instead.

```tsx
// ❌ Primitive — no semantic meaning
className="bg-blue-500"

// ✅ Semantic — intent is explicit
className="bg-[var(--colors-bg-system-info-primary)]"
```

If the needed semantic token exists only under a different Tailwind category (e.g., a `borderColor` token used as a background), reference the CSS variable directly: `bg-[var(--colors-border-system-info-primary)]`. This is preferable to a primitive class because the value still tracks the semantic token system.

---

## 3. Interaction States

### Rule 3.1 — All interaction states via Tailwind modifiers [Critical]

Never recreate hover, focus, or active in JavaScript:

| State | Tailwind | Common mistakes |
|-------|----------|----------------|
| Hover | `hover:bg-brand-secondary` | `onMouseEnter`/`onMouseLeave` + state |
| Focus | `focus-visible:ring-2 focus-visible:ring-focus` | `onFocus`/`onBlur` + state, or `focus:` showing ring on mouse click |
| Active | `active:bg-brand-tertiary` | `state="Pressed"` prop |
| Checked | `peer-checked:bg-brand-primary` (on sibling of hidden input) | `checked && "bg-brand-primary"` via JS ternary |

For form controls with hidden inputs: `peer` on the input, `peer-checked:`, `peer-focus-visible:`, `peer-disabled:` on visual sibling. `group` on the label wrapper, `group-hover:` on visual sibling.

**Exception for checked-state icon visibility:** conditional rendering of the icon via `checked` prop is acceptable (`{checked && <CheckIcon />}`). Tailwind's `peer-checked:` can toggle opacity/scale but cannot conditionally render React elements.

### Rule 3.2 — Disabled via native attribute [Critical]

Use native HTML `disabled`, not a custom prop or CSS class.

```tsx
// ❌
<div className={cn("...", isDisabled && "opacity-40 pointer-events-none")}>

// ✅
<button disabled={disabled} className="disabled:opacity-40 disabled:cursor-not-allowed">
```

`disabled:pointer-events-none` is NOT needed — native `disabled` already blocks events.

### Rule 3.3 — Focus ring consistent with project [Warning]

Check CLAUDE.md for the project's focus ring convention. Typically:

**Direct elements (buttons, links):**
```
focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-focus focus-visible:ring-offset-2
```

**Hidden input siblings (checkbox, radio, switch):**
```
peer-focus-visible:ring-2 peer-focus-visible:ring-focus peer-focus-visible:ring-offset-2
```

The component must match. Mixing `outline` and `ring-*` within the same project is a finding.

---

## 4. Accessibility

### Rule 4.1 — Use native HTML elements [Critical]

If a native element exists for the behavior, use it.

| Behavior | Required element |
|----------|-----------------|
| Triggers an action | `<button>` |
| Toggles on/off | `<input type="checkbox">` |
| Picks one from set | `<input type="radio">` |
| Text input | `<input>` or `<textarea>` |
| Navigation link | `<a href="...">` |
| Selection from list | `<select>` |
| Modal/dialog | `<dialog>` |

### Rule 4.2 — Label association [Critical]

Every form control needs an accessible label — wrapping `<label>` or `htmlFor`+`id`. Use `useId()` for stable IDs.

### Rule 4.3 — Decorative icons hidden [Warning]

`<svg aria-hidden="true">` or icon component with `aria-hidden="true"`.

### Rule 4.4 — Keyboard interaction [Critical]

| Component | Tab | Space | Enter | Arrow keys |
|-----------|-----|-------|-------|------------|
| Button | Focus | Activate | Activate | — |
| Checkbox | Focus | Toggle | — | — |
| Radio group | Focus group | Select | — | Move selection |
| Switch | Focus | Toggle | — | — |
| Text input | Focus | Type | Submit form | Move cursor |

Native HTML elements provide this for free.

### Rule 4.5 — ARIA attributes when needed [Warning]

- Switch: `role="switch"` on `<input type="checkbox">`
- Indeterminate: `aria-checked="mixed"` + `.indeterminate = true` via ref
- Toggle button: `aria-pressed`
- Loading button: `aria-busy="true"`
- Error input: `aria-invalid="true"` + `aria-describedby` → error message


### Rule 4.6 — Timed components must be pausable (WCAG 2.2.1) [Critical]

Components that auto-dismiss on a timer (toast, alert with countdown, notification banner) must let users pause or extend the time limit.

**Required behaviors:**
- Pause countdown on `mouseenter` and `focus` on the component
- Resume on `mouseleave` and `blur`
- Provide a visible dismiss button (not timer-only removal)

```tsx
// ❌ Auto-dismisses with no user control
useEffect(() => {
  setTimeout(onDismiss, 5000);
}, []);

// ✅ Pauses on hover and focus
<div
  role="alert"
  onMouseEnter={pauseCountdown}
  onMouseLeave={resumeCountdown}
  onFocus={pauseCountdown}
  onBlur={resumeCountdown}
>
```

WCAG 2.2.1 is a Level A success criterion. If the component has an action button (e.g., "Undo"), the user must have enough time to reach and activate it — otherwise the action is inaccessible to keyboard and screen reader users.

### Rule 4.7 — Contextual aria-labels for repeated actions [Warning]

When a component is designed to appear multiple times on screen (alert, toast, card, list item), interactive children (dismiss button, edit link, delete icon) must include context in their accessible name.

```tsx
// ❌ Generic — ambiguous with multiple instances on page
<button aria-label="Dismiss">

// ✅ Contextual — distinguishable by screen reader
<button aria-label={title ? `Dismiss: ${title}` : "Dismiss alert"}>
```

Icon-only buttons with static `aria-label` in a repeatable component are a finding. The label should incorporate a prop value (title, name, index) that varies per instance.

### Rule 4.8 — Overlay must prevent body scroll [Warning]

When an overlay component (modal, dialog, drawer, bottom sheet) is open, the page behind it must not scroll. Native `<dialog>` with `showModal()` blocks pointer interaction but does **not** prevent scroll on all browsers (notably Safari iOS).

```tsx
// ❌ No scroll lock — body scrolls behind open dialog
useEffect(() => {
  if (open) dialog.showModal();
}, [open]);

// ✅ Body scroll prevented while open
useEffect(() => {
  if (!open) return;
  const prev = document.body.style.overflow;
  document.body.style.overflow = "hidden";
  return () => { document.body.style.overflow = prev; };
}, [open]);
```

This applies to: modal dialog, drawer, bottom sheet, lightbox, command palette — any component that renders a full-viewport overlay.

---

## 5. Architecture & Scalability

### Rule 5.1 — No external layout opinions [Warning]

No fixed `width`, no `margin` on root. The consumer controls external spacing.

### Rule 5.2 — SSR safety [Critical]

No bare `document`/`window` in render path. No `Math.random()` for IDs — use `useId()`.

Note: pure Tailwind components (no `<style>` injection, no DOM measurement) are SSR-safe by default. This rule mainly catches leftover browser API usage from pre-Tailwind code.

### Rule 5.3 — Composition and defensive coding [Warning]

- 10+ props or multi-branch render → consider compound components.
- `undefined` props, empty labels, unexpected variant values — must not crash or render "undefined".
- `cva` `defaultVariants` should cover the zero-props case.

### Rule 5.4 — File structure matches project [Warning]

Expected order in a component file:
1. Header comment
2. Imports (React, cva, cn, icons)
3. Variant definitions (`cva`)
4. Exported types/interface
5. Component with `forwardRef`
6. Default export

### Rule 5.5 — No unnecessary re-renders [Suggestion]

Only flag for complex components. Watch: callbacks without `useCallback`, expensive computations without `useMemo`.

---

## Systemic Findings

Findings that apply to ALL components, not just the one under audit. Two paths:

- **Already in anti-patterns.md** → status "Confirmed ✓" — no action, just note it in audit file
- **New pattern** → status "→ PROMOTE" — propose to user: "Should I add this to anti-patterns.md?" Wait for approval before adding.

Systemic findings are not a severity level — they have their own severity (Warning, Critical, etc.) AND the Systemic tag.
