# Refactor Playbook

One change at a time, verify after each. Never mix a props API change with a styling restructure with a token replacement.

---

## Refactor Order

```
Phase 1: Foundation (Critical)
  1.1  Native HTML element + accessibility
  1.2  Props API redesign
  1.3  forwardRef
  1.4  onChange + controlled/uncontrolled

Phase 2: Styling (Warning)
  2.1  Inline styles / custom CSS → Tailwind utilities
  2.2  Interaction states → Tailwind modifiers
  2.3  Hardcoded values → token-mapped classes
  2.4  className merging → cn()
  2.5  Variant logic → cva (when appropriate)
  2.6  Icons → currentColor

Phase 3: Polish (Suggestion)
  3.1  Naming consistency
  3.2  Remove fixed width/margin
  3.3  Transition specificity
```

---

## Phase 1: Foundation

These changes affect the public API and are often breaking changes.

### 1.1 Native HTML element + accessibility

1. Identify the interaction archetype (button, checkbox, radio, input, etc.)
2. Replace root with the correct native element
3. For form controls: add hidden native `<input>` with `className="peer sr-only"`
4. Ensure label association via wrapping `<label>` or `htmlFor`/`id`
5. Add ARIA attributes where needed (`role="switch"`, `aria-pressed`, etc.)
6. `aria-hidden="true"` on decorative icons

**Verify:** Tab → Space/Enter activates. Screen reader announces role and state.

### 1.2 Props API redesign

1. `extends [NativeHTMLAttributes]` on the interface
2. Add `VariantProps<typeof componentVariants>` intersection if using cva
3. Remove `state` prop — hover/focus/active become Tailwind modifiers
4. `disabled` from native attr, `loading` as boolean prop
5. String enums → booleans
6. `label` accepts `ReactNode`
7. `...rest` spread to native element

**Verify:** `<Component {...register("field")} />` works with React Hook Form.

### 1.3 forwardRef

1. `forwardRef<HTMLElementType, ComponentProps>`
2. `ref` → native element
3. `useId()` for label association, allow external `id` override

**Verify:** `ref.current` is the native element.

### 1.4 onChange + controlled/uncontrolled

1. Remove custom `onChange: (value: T) => void`
2. Standard `onChange` from `InputHTMLAttributes`
3. Support `defaultValue`/`defaultChecked` for uncontrolled mode

**Verify:** Controlled with React Hook Form, uncontrolled with `defaultValue`.

---

## Phase 2: Styling

Structural changes that don't affect the public API.

### 2.1 Inline styles / custom CSS → Tailwind utilities

1. Find all `style={{}}` with static values — replace with equivalent Tailwind classes
2. Find any `.css` files or `<style>` tags — replace with Tailwind utilities in className
3. Find any custom CSS classes mixed with Tailwind — replace with pure utilities
4. Keep `style={{}}` ONLY for truly dynamic values (calculated positions, progress %)

```tsx
// Before
<div style={{ borderRadius: "8px", padding: "0 6px" }} className="custom-box">

// After
<div className="rounded-sm px-1.5">
```

**Verify:** Visual appearance unchanged. No `.css` files, no `<style>` tags, no `style={{}}` for static values.

### 2.2 Interaction states → Tailwind modifiers

1. Remove `onMouseEnter`/`onMouseLeave`, `onFocus`/`onBlur` state tracking
2. Remove `useState` for hover/focus/active states
3. Replace with Tailwind modifiers on the element:

```tsx
// Before
const [hovered, setHovered] = useState(false);
<button onMouseEnter={() => setHovered(true)} onMouseLeave={() => setHovered(false)}
  className={hovered ? "bg-brand-secondary" : "bg-brand-primary"}>

// After
<button className="bg-brand-primary hover:bg-brand-secondary">
```

4. For hidden inputs: add `peer` class, use `peer-checked:`, `peer-focus-visible:` on visual sibling
5. Remove `state` prop, remove style-switching helpers

**Verify:** Hover → color change. Tab → focus ring. Click+hold → active state. Disable → blocked + faded.

### 2.3 Hardcoded values → token-mapped classes

1. Search for `bg-[#`, `text-[#`, `border-[#`, `text-[14px]`, `p-[6px]`, `rounded-[8px]`
2. Find the matching Tailwind class from `tailwind.config.ts`
3. Values without matching classes: flag to the user — config may need extending

```tsx
// Before
className="bg-[#00ec88] text-[14px] rounded-[8px]"

// After
className="bg-brand-primary text-body-3 rounded-sm"
```

**Verify:** Visual appearance unchanged. Zero arbitrary values except documented structural exceptions.

### 2.4 className merging → cn()

1. Find string concatenation for className: template literals, `+` operator, ternaries without `cn()`
2. Replace with `cn()` from `lib/utils`
3. Ensure consumer `className` prop is the last argument (wins in conflict resolution)

```tsx
// Before
className={`bg-brand-primary ${disabled ? "opacity-40" : ""} ${className || ""}`}

// After
className={cn("bg-brand-primary", disabled && "opacity-40", className)}
```

**Verify:** Consumer can pass `className="bg-red-500"` and it overrides the default background.

### 2.5 Variant logic → cva (when appropriate)

Only for components with 2+ variant dimensions. If the component has manual ternary chains for variant × size combinations:

1. Extract base classes + variant classes into `cva` definition
2. Add `VariantProps<typeof variants>` to the props interface
3. Set `defaultVariants`
4. Use `cn(variants({ variant, size }), className)` in the component

Skip cva for components with 0–1 variant dimensions — `cn()` alone is sufficient.

**Verify:** All variant combinations render correctly. `<Component />` with zero props renders the default.

### 2.6 Icons → currentColor

Replace `stroke="#..."`, `fill="#..."` with `currentColor`. Remove color props on icon components. Color is now controlled by the parent's `text-*` Tailwind class.

---

## Phase 3: Polish

Low-risk, any order:
- Naming consistency with project convention
- Remove fixed width/margin on root
- Replace `transition-all` with `transition-colors` / `transition-opacity`

---

## Breaking Changes

Document every public API change:

```
### Props renamed
- `type` → `variant`

### Props removed
- `state` → `disabled` (native) + Tailwind modifiers + `loading` (boolean)

### Props type changed
- `isChecked: "No"|"Yes"` → `checked: boolean` + `indeterminate: boolean`
- `onChange: (value) => void` → native `ChangeEventHandler`

### Migration
// Before                                    // After
<Button type="Primary" state="Disabled" />   <Button variant="primary" disabled />
<Checkbox isChecked="Yes" />                 <Checkbox checked />
```

---

## Verification Checklist

Run the full checklist from SKILL.md Step 3. Every item must pass. Additionally:

- [ ] Every breaking change documented with before/after
- [ ] Component visually matches original in every variant/state
- [ ] No functionality lost
- [ ] Component is simpler (fewer lines, fewer helpers, fewer props)
- [ ] Edge cases handled: `undefined` props, empty labels, unexpected values
