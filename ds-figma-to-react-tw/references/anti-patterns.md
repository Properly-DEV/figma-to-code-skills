# Anti-Patterns Checklist

Final check before delivering a component. Fix any of these before delivering.

---

## 1. State prop for interaction states

```tsx
// ❌ Figma variant exported as prop
<Checkbox state="Hovered" />
<Button state="Focused" />

// ✅ Interaction states handled by Tailwind modifiers
className="hover:bg-brand-secondary focus-visible:ring-2 active:bg-brand-tertiary"
```

**Why it's wrong:** Hover, focus, and active are browser events. Exposing them as props means the component doesn't respond to real user interaction — it just renders a static snapshot of a Figma variant.

---

## 2. Inline styles for static values

```tsx
// ❌ Style objects for values that never change
<div style={{ borderRadius: "8px", padding: "0 6px", color: "#595959" }}>

// ✅ Tailwind utility classes
<div className="rounded-sm px-1.5 text-secondary">
```

**Why it's wrong:** Inline styles can't be overridden via `className`, can't use responsive modifiers, can't use state modifiers (`hover:`, `focus:`), and bypass Tailwind's design token system entirely.

---

## 3. String enum instead of boolean

```tsx
// ❌ Figma naming leaked into code
isChecked: "No" | "Yes" | "Indeterminate"

// ✅ Standard React/HTML types
checked?: boolean
indeterminate?: boolean
```

**Why it's wrong:** Native HTML uses `checked` (boolean). React Hook Form and every form library expect boolean checked and standard `ChangeEvent`. String enums break all integrations.

---

## 4. Missing native HTML element

```tsx
// ❌ Custom div pretending to be a checkbox
<div onClick={handleClick} className="size-5 rounded border">
  {isChecked && <CheckIcon />}
</div>

// ✅ Real native input — keyboard, forms, a11y for free
<input type="checkbox" className="peer sr-only" checked={checked} onChange={onChange} />
<span className="peer-checked:bg-brand-primary" aria-hidden="true">
  {checked && <CheckIcon />}
</span>
```

**Why it's wrong:** Without a native `<input>`, no keyboard support, no form submission, no screen reader semantics, no browser autofill.

---

## 5. Missing forwardRef

```tsx
// ❌
function Button(props: ButtonProps) { return <button>...</button>; }

// ✅
export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  function Button(props, ref) { return <button ref={ref}>...</button>; }
);
```

**Why it's wrong:** Without forwardRef, React Hook Form can't register the field, and imperative access (`.focus()`, `.indeterminate`) is impossible.

---

## 6. Hardcoded values in className

```tsx
// ❌ Raw hex/px values — bypasses the token system
className="bg-[#00ec88] text-[14px] rounded-[8px] p-[6px]"

// ✅ Tailwind classes mapped to design tokens
className="bg-brand-primary text-body-3 rounded-sm px-1.5"
```

**Why it's wrong:** Tailwind's arbitrary value syntax (`[#hex]`, `[14px]`) is an escape hatch, not a feature. It bypasses design tokens — when the brand color changes in tokens.css, hardcoded values stay wrong. If a value doesn't have a Tailwind class, the token or config is incomplete — fix the source, don't hardcode.

---

## 7. Custom CSS classes alongside Tailwind

```tsx
// ❌ Custom CSS class for something Tailwind can express
// styles.css: .button-primary { background-color: var(--colors-bg-brand-primary); }
className="button-primary rounded-sm"

// ✅ Pure Tailwind utilities
className="bg-brand-primary rounded-sm"
```

**Why it's wrong:** Mixing custom CSS with Tailwind utilities creates two styling systems. Developers don't know where to look. `tailwind-merge` inside `cn()` can't resolve conflicts between custom classes and utilities. One system, no exceptions.

---

## 8. Missing cn() for className merging

```tsx
// ❌ String concatenation — no conflict resolution, fragile
className={`bg-brand-primary ${disabled ? "opacity-40" : ""} ${className}`}

// ✅ cn() handles conditionals and resolves conflicts
className={cn(buttonVariants({ variant, size }), className)}
```

**Why it's wrong:** String concatenation doesn't resolve Tailwind class conflicts. If a consumer passes `className="bg-red-500"` to override the background, both `bg-brand-primary` AND `bg-red-500` end up in the class list — the browser picks one unpredictably. `cn()` (via `tailwind-merge`) drops the losing class.

---

## 9. Custom onChange signature

```tsx
// ❌ Non-standard callback
onChange: (value: boolean) => void

// ✅ Standard React event
onChange?: React.ChangeEventHandler<HTMLInputElement>
```

**Why it's wrong:** React Hook Form, Formik, and every form library pass their own `onChange` handler that expects a standard `React.ChangeEvent`. Custom signatures break this.

---

## 10. Label as string-only

```tsx
// ❌
label?: string

// ✅
label?: React.ReactNode
```

**Why it's wrong:** Labels sometimes need formatting (bold, link, icon). `ReactNode` accepts strings and rich content.

---

## 11. Component controls its own layout

```tsx
// ❌ Component dictates spacing and width
<button className="bg-brand-primary rounded-sm mt-4 w-[290px]">

// ✅ Component has no layout opinion — parent controls it
<div className="mt-4 w-72">
  <Button variant="primary">Submit</Button>
</div>
```

**Why it's wrong:** Fixed width and margin make the component impossible to reuse in different layouts. Let the consumer control external spacing.

---

## 12. transition-all instead of explicit properties

```tsx
// ❌ Animates everything including layout properties
className="transition-all duration-150"

// ✅ Explicit — only what needs to animate
className="transition-colors duration-150"
```

**Why it's wrong:** `transition-all` animates width, height, padding — causing layout thrashing. Use `transition-colors`, `transition-opacity`, or `transition-transform`.

---

## 13. Overcomplicating cva for simple components

```tsx
// ❌ cva with a single variant that has one value
const dividerVariants = cva("h-px bg-tertiary");
// This adds cva import and VariantProps for zero benefit

// ✅ cn() directly — no cva overhead
<div className={cn("h-px bg-tertiary", className)} />
```

**Why it's wrong:** cva is for components with variant matrices. Using it on a zero-variant component adds import weight and cognitive load without benefit.

---

## 14. Primitive Tailwind color instead of semantic token

```tsx
// ❌ Valid Tailwind class but maps to a primitive palette color
className="bg-blue-500 text-red-600 border-green-600"

// ✅ Semantic CSS variable — intent is explicit, survives palette changes
className="bg-[var(--colors-bg-system-info-primary)] text-system-error-primary border-system-success-secondary"
```

**Why it's wrong:** `bg-blue-500` resolves to a fixed palette color, not a semantic decision. If the design system redefines "info" from blue to teal, primitive tokens stay blue. Semantic tokens (`--colors-*-system-*`) are the contract between design and code — they carry meaning, not just a hex value. When no semantic Tailwind class exists for the intended use (e.g., a border color used as a background), reference the CSS variable directly via `bg-[var(--colors-border-*)]` rather than falling back to a primitive. This is distinct from anti-pattern #6 (hardcoded hex): here the class is valid Tailwind, but it bypasses the semantic layer.

---

## 15. Generic aria-label on repeated component

```tsx
// ❌ Identical label on every instance — ambiguous when multiples exist
<button aria-label="Dismiss alert" onClick={onDismiss}>
  <XIcon />
</button>

// ✅ Contextual label — unique per instance
<button aria-label={title ? `Dismiss: ${title}` : "Dismiss alert"} onClick={onDismiss}>
  <XIcon />
</button>
```

**Why it's wrong:** When multiple alerts (or toasts, cards, list items) are on screen, screen readers list all actions by label. Three identical "Dismiss alert" buttons are indistinguishable. Include context from the parent component — the title, type, or position — so each label is unique. Applies to any repeated action: "Remove", "Edit", "Delete", "Close".

---

## 16. Unstable callback prop in useEffect dependency chain

```tsx
// ❌ onDismiss changes identity every render → timer restarts endlessly
const handleDismiss = useCallback(() => {
  onDismiss?.();
}, [onDismiss]);

useEffect(() => {
  timerRef.current = setTimeout(handleDismiss, countdown * 1000);
  return () => clearTimeout(timerRef.current);
}, [countdown, handleDismiss]);

// ✅ Store callback in a ref — effect depends only on stable values
const onDismissRef = useRef(onDismiss);
onDismissRef.current = onDismiss;

const handleDismiss = useCallback(() => {
  onDismissRef.current?.();
}, []);

useEffect(() => {
  timerRef.current = setTimeout(handleDismiss, countdown * 1000);
  return () => clearTimeout(timerRef.current);
}, [countdown, handleDismiss]);
```

**Why it's wrong:** When a parent passes an inline callback (`onDismiss={() => setVisible(false)}`), a new function reference is created every render. If that callback flows into a `useCallback` → `useEffect` dependency chain, the effect re-runs on every parent render — restarting timers, animations, or subscriptions from scratch. The component may never complete its timed action. Store the callback in a ref to break the dependency chain while always calling the latest version.

---

## 17. Dangling aria-labelledby / aria-describedby

```tsx
// ❌ aria-describedby always set — but the target element is conditional
<dialog aria-describedby={descriptionId}>
  <DialogHeader title="Settings" />  {/* no description → no element with descriptionId */}
</dialog>

// ✅ Only set the attribute when the target element exists
<dialog aria-describedby={hasDescription ? descriptionId : undefined}>
  ...
</dialog>
```

**Why it's wrong:** When `aria-labelledby` or `aria-describedby` points to an ID that doesn't exist in the DOM, it's an invalid ARIA reference. Screen readers silently ignore it, but it fails accessibility audits and signals broken wiring. In compound components where sub-components conditionally render the labelling elements, the parent must track whether those elements are actually mounted before setting the ARIA attribute.

---

## 18. Real interactive elements inside ARIA role containers

When a component uses `role="option"`, `role="menuitem"`, or similar ARIA roles, **never nest real interactive elements** (`<input>`, `<button>`, `<select>`) inside — even if visually hidden. Screen readers announce both the ARIA role AND the nested element, creating confusing double announcements.

Use `aria-hidden="true"` on the interactive child, or use a visual-only indicator. The parent's ARIA attributes (`aria-selected`, `aria-checked`) carry the semantic state.

❌ Bad:
```tsx
<div role="option" aria-selected={checked}>
  <Checkbox checked={checked} />  {/* renders <input type="checkbox"> */}
  <span>Option label</span>
</div>
```

Screen reader announces: "Option selected" + "Checkbox checked" — double noise.

✅ Good:
```tsx
<div role="option" aria-selected={checked} tabIndex={-1}>
  <span aria-hidden="true">
    <Checkbox checked={checked} readOnly className="pointer-events-none" />
  </span>
  <span>Option label</span>
</div>
```

Screen reader announces only: "Option selected" — clean, single announcement.
