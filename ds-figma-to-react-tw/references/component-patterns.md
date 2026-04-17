# Component Patterns — Interaction Archetypes

> **Tailwind note:** HTML structures and ARIA attributes in this file are universal and apply as-is. Class names (`.rly-{component}__element`) are structural placeholders — replace with Tailwind utility classes following the patterns in SKILL.md Step 6. CSS blocks (sibling selectors, pseudo-classes) translate to Tailwind `peer-*`, `group-*`, and state modifiers.

Every UI component, regardless of its name or visual design, belongs to one or more interaction archetypes. This file organizes patterns by **how the component behaves**, not what it looks like. A "toggle chip", a "switch", and a "favorite heart button" are all the same archetype — Toggle.

When building a component, find its archetype here. If a component combines two archetypes (e.g., a dropdown = Action Trigger + Disclosure + Single Selection), read all relevant sections and combine their patterns.

## Table of Contents

1. [Toggle](#1-toggle) — on/off binary state
2. [Single Selection](#2-single-selection) — pick one from many
3. [Multi Selection](#3-multi-selection) — pick many from many
4. [Text Entry](#4-text-entry) — user types or edits content
5. [Action Trigger](#5-action-trigger) — fires an event on click
6. [Disclosure](#6-disclosure) — show/hide related content
7. [Overlay](#7-overlay) — content floating above the page
8. [Display](#8-display) — non-interactive visual information
9. [Composite Components](#9-composite-components) — combining archetypes

---

## 1. Toggle

**What it does:** Switches between two states — on/off, yes/no, expanded/collapsed.

**Examples:** Checkbox, switch, toggle button, favorite/bookmark icon, expand/collapse trigger, "mark as read", theme toggle (light/dark).

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Labeled toggle in a form | `<input type="checkbox">` | Native form submission, checked state, label association |
| Toggle with on/off semantics | `<input type="checkbox" role="switch">` | Screen readers announce "on/off" instead of "checked/unchecked" |
| Toggle button (stays pressed) | `<button aria-pressed="true/false">` | When the toggle is visually a button, not a form field |

### ARIA

- Checkbox: no extra role needed, native semantics are correct
- Switch: add `role="switch"` to `<input type="checkbox">`
- Toggle button: use `aria-pressed="true"` / `aria-pressed="false"`
- Indeterminate checkbox: `aria-checked="mixed"` (set `.indeterminate = true` via JS ref — no HTML attribute exists)

### Keyboard

- `Space` toggles the state
- `Tab` moves focus in/out
- These are native behaviors — no JavaScript needed if using the correct HTML element

### Structure (form toggle — checkbox/switch)

```tsx
<label className="rly-{component}">
  <input
    ref={ref}
    type="checkbox"
    role={isSwitch ? "switch" : undefined}
    className="rly-{component}__input"  /* visually hidden */
    checked={checked}
    disabled={disabled}
    aria-checked={indeterminate ? "mixed" : undefined}
    {...rest}
  />
  <span className="rly-{component}__{visual}" aria-hidden="true">
    {/* Visual indicator — box, track+thumb, icon, etc. */}
  </span>
  {label && <span className="rly-{component}__label">{label}</span>}
</label>
```

### Structure (toggle button)

```tsx
<button
  ref={ref}
  className="rly-{component}"
  aria-pressed={pressed}
  disabled={disabled}
  {...rest}
>
  {icon}
  {label && <span>{label}</span>}
</button>
```

### CSS pattern

The hidden native input drives all visual states via sibling selectors:

```css
.rly-{component}__input:checked ~ .rly-{component}__{visual} { /* on state */ }
.rly-{component}:hover .rly-{component}__input:not(:disabled) ~ .rly-{component}__{visual} { /* hover */ }
.rly-{component}__input:focus-visible ~ .rly-{component}__{visual}::after { /* focus ring */ }
.rly-{component}__input:disabled ~ .rly-{component}__{visual} { /* disabled visual */ }
```

### Props pattern

```tsx
// Extends native input — checked, onChange, disabled, name, value come for free
interface ToggleProps extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'type'> {
  label?: React.ReactNode;
  hideLabel?: boolean;
  // Add component-specific props only when native attrs don't cover them:
  indeterminate?: boolean;  // checkbox only, JS-only property
}
```

---

## 2. Single Selection

**What it does:** User picks exactly one option from a set.

**Examples:** Radio group, select/dropdown, segmented control, tab bar, single-select listbox, date picker (single date), rating stars, toggle group (only one active).

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Small set, all visible | `<input type="radio">` in a `<fieldset>` | Native grouping, arrow key navigation, form submission |
| Native dropdown | `<select>` | Simplest, fully accessible, browser-styled |
| Custom dropdown | `<button>` triggering `<ul role="listbox">` | When you need custom styling on options |
| Tabs | `<div role="tablist">` + `<button role="tab">` + `<div role="tabpanel">` | Tab bar pattern with panel association |
| Segmented control | `<div role="radiogroup">` + `<button role="radio">` | Visually buttons, semantically radio group |

### ARIA

- Radio group: `<fieldset>` + `<legend>` wraps the group. Each radio's label is its `<label>`.
- Custom listbox: `role="listbox"` on the list, `role="option"` on each item, `aria-selected="true"` on the active one, `aria-activedescendant` on the trigger to announce the focused option.
- Tabs: `aria-selected="true"` on the active tab, `aria-controls` linking tab to panel, `aria-labelledby` linking panel back to tab, `tabindex="0"` on active tab, `tabindex="-1"` on inactive tabs.

### Keyboard

- **Radio group:** Arrow keys move selection between options. Tab moves focus into/out of the group as a whole (not between individual radios). Space selects the focused option.
- **Custom dropdown:** Enter/Space opens the list. Arrow keys navigate options. Enter selects. Escape closes. Home/End jump to first/last. Typeahead (typing a letter jumps to matching option).
- **Tabs:** Arrow Left/Right moves between tabs. Tab key moves focus from tab bar into the active panel. Home/End jump to first/last tab.

### Structure (radio group)

```tsx
<fieldset className="rly-{component}">
  <legend className="rly-{component}__legend">{groupLabel}</legend>
  {options.map(option => (
    <label key={option.value} className="rly-{component}__option">
      <input
        type="radio"
        className="rly-{component}__input"
        name={name}
        value={option.value}
        checked={value === option.value}
        disabled={disabled || option.disabled}
        onChange={onChange}
      />
      <span className="rly-{component}__indicator" aria-hidden="true" />
      <span>{option.label}</span>
    </label>
  ))}
</fieldset>
```

### Structure (custom dropdown)

```tsx
<div className="rly-{component}" ref={wrapperRef}>
  <button
    ref={ref}
    className="rly-{component}__trigger"
    aria-haspopup="listbox"
    aria-expanded={open}
    aria-labelledby={labelId}
    onClick={toggleOpen}
    onKeyDown={handleKeyDown}
  >
    <span>{selectedLabel ?? placeholder}</span>
    <ChevronIcon />
  </button>
  {open && (
    <ul role="listbox" className="rly-{component}__list" aria-labelledby={labelId}>
      {options.map(option => (
        <li
          key={option.value}
          role="option"
          aria-selected={value === option.value}
          className="rly-{component}__option"
          onClick={() => select(option.value)}
        >
          {option.label}
        </li>
      ))}
    </ul>
  )}
</div>
```

### Structure (tabs)

```tsx
<div className="rly-tabs">
  <div role="tablist" aria-label={ariaLabel}>
    {tabs.map((tab, i) => (
      <button
        key={tab.id}
        role="tab"
        id={`tab-${tab.id}`}
        aria-selected={activeTab === tab.id}
        aria-controls={`panel-${tab.id}`}
        tabIndex={activeTab === tab.id ? 0 : -1}
        onClick={() => setActiveTab(tab.id)}
        onKeyDown={handleTabKeyDown}
      >
        {tab.label}
      </button>
    ))}
  </div>
  {tabs.map(tab => (
    <div
      key={tab.id}
      role="tabpanel"
      id={`panel-${tab.id}`}
      aria-labelledby={`tab-${tab.id}`}
      hidden={activeTab !== tab.id}
    >
      {tab.content}
    </div>
  ))}
</div>
```

### CSS pattern

Radio uses sibling selectors like Toggle. Custom dropdowns and tabs use class modifiers since state is managed in React:

```css
/* Radio — same sibling selector pattern as Toggle */
.rly-{component}__input:checked ~ .rly-{component}__indicator { /* selected */ }

/* Dropdown trigger */
.rly-{component}__trigger[aria-expanded="true"] { /* open state styling */ }

/* Tab — active state */
[role="tab"][aria-selected="true"] { /* active tab */ }
[role="tab"]:focus-visible { /* focus ring on tab */ }
```

---

## 3. Multi Selection

**What it does:** User picks zero or more options from a set.

**Examples:** Checkbox group, multi-select dropdown, tag/chip picker, transfer list, filter bar, permission matrix.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| All options visible | `<fieldset>` of `<input type="checkbox">` | Each checkbox is independent, fieldset groups them |
| Dropdown with multi-select | `<button>` + `<ul role="listbox" aria-multiselectable="true">` | Custom dropdown with multiple selection |
| Tag picker | Combination: text input for search + listbox for options + chip display for selected | Composite pattern |

### ARIA

- Checkbox group: `<fieldset>` + `<legend>`. Each checkbox operates independently.
- Multi-select listbox: `aria-multiselectable="true"` on the listbox. `aria-selected="true/false"` on each option.
- "Select all" checkbox controlling a group: should be `aria-checked="mixed"` (indeterminate) when some but not all children are selected.

### Keyboard

- **Checkbox group:** Tab moves between checkboxes. Space toggles the focused one.
- **Multi-select listbox:** Arrow keys navigate. Space toggles selection on focused option. Shift+Click for range selection (optional). Ctrl+A selects all (optional).

### Structure (checkbox group)

```tsx
<fieldset className="rly-{component}">
  <legend>{groupLabel}</legend>
  {options.map(option => (
    <label key={option.value} className="rly-{component}__item">
      <input
        type="checkbox"
        className="rly-{component}__input"
        name={`${name}[]`}
        value={option.value}
        checked={selectedValues.includes(option.value)}
        onChange={handleChange}
      />
      <span className="rly-{component}__box" aria-hidden="true">
        <CheckIcon />
      </span>
      <span>{option.label}</span>
    </label>
  ))}
</fieldset>
```

### Props pattern

```tsx
// Group component manages the set of selected values
interface CheckboxGroupProps {
  name: string;
  options: Array<{ value: string; label: React.ReactNode; disabled?: boolean }>;
  value?: string[];
  defaultValue?: string[];
  onChange?: (values: string[]) => void;
  label: string;  // used as <legend>
  disabled?: boolean;
}
```

---

## 4. Text Entry

**What it does:** User provides text, numbers, or other typed content.

**Examples:** Text input, textarea, search field, password input, number input, date input, email input, phone input, OTP/PIN input, editable label/inline edit, combobox (input + suggestions), rich text editor.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Single line | `<input type="text/email/password/number/tel/url/search/date">` | Use the most specific type for mobile keyboard and validation |
| Multi line | `<textarea>` | Native multiline with resize |
| Input + suggestions | `<input role="combobox">` + `<ul role="listbox">` | Autocomplete / typeahead pattern |

### ARIA

- Label association: `<label htmlFor={id}>` or wrapping `<label>`. Never rely on placeholder as the label.
- Error state: `aria-invalid="true"` on the input.
- Error message: connect via `aria-describedby={errorId}`. Use `role="alert"` on the error element for live announcement.
- Helper text: also connected via `aria-describedby`. If both helper and error exist, concatenate IDs: `aria-describedby="helper-id error-id"`.
- Required: `aria-required="true"` or native `required` attribute.
- Character count / constraints: `aria-describedby` pointing to a live region.
- Combobox: `role="combobox"`, `aria-expanded`, `aria-autocomplete="list"`, `aria-activedescendant`.

### Keyboard

- Standard typing behavior (native)
- `Tab` moves focus in/out
- `Escape` can clear the field or close suggestions (combobox)
- `Enter` submits the form (single-line input) or adds a newline (textarea)
- Combobox: Arrow Down opens/navigates suggestions, Enter selects, Escape closes

### Structure (input with label and error)

```tsx
<div className={cn("rly-{component}", disabled && "rly-{component}--disabled")}>
  {showLabel && (
    <label htmlFor={id} className="rly-{component}__label">
      {label}
    </label>
  )}
  <div className="rly-{component}__field">
    <input
      ref={ref}
      id={id}
      className="rly-{component}__native"
      type={type}
      disabled={disabled}
      aria-invalid={!!error || undefined}
      aria-describedby={descriptionIds || undefined}
      aria-required={required || undefined}
      {...rest}
    />
    {/* Optional: trailing icon, clear button, etc. */}
  </div>
  {error && errorMessage && (
    <p id={errorId} className="rly-{component}__error" role="alert">
      {errorMessage}
    </p>
  )}
  {helperText && !error && (
    <p id={helperId} className="rly-{component}__helper">
      {helperText}
    </p>
  )}
</div>
```

### CSS pattern

Input uses `:focus-within` on the field wrapper (not sibling selectors) because the native `<input>` is inside the wrapper, not a sibling:

```css
.rly-{component}__field { border: 1px solid var(--colors-border-tertiary); }
.rly-{component}__field:hover { border-color: var(--colors-border-secondary); }
.rly-{component}__field:focus-within { border-color: var(--colors-border-secondary); }
.rly-{component}__field:focus-within::after { /* focus ring */ }
.rly-{component}--error .rly-{component}__field { border-color: var(--colors-border-system-error-primary); }
```

---

## 5. Action Trigger

**What it does:** Fires an event, navigates, or initiates a process when clicked.

**Examples:** Button, icon button, link button, FAB (floating action button), menu item, split button, CTA, submit button, "load more" button, social login button, copy button.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Triggers an action | `<button>` | Native focus, keyboard, disabled, form submit |
| Navigates to URL | `<a href="...">` | Semantic navigation, right-click "open in new tab", crawlable |
| Submits a form | `<button type="submit">` | Native form submission |
| Icon only (no text) | `<button aria-label="...">` | Must have accessible name |
| Link styled as button | `<a>` with button styling | Still an `<a>` if it navigates |

**Key decision:** If it goes somewhere → `<a>`. If it does something → `<button>`. Never the other way around.

### ARIA

- Loading state: `aria-busy="true"` on the button, hide content with `opacity: 0` (not `display: none` — preserves layout size)
- Icon-only button: **must** have `aria-label` — this is non-negotiable. An icon button without a text label is invisible to screen readers.
- Disabled but focusable (rare): `aria-disabled="true"` instead of native `disabled`. Use when you need to show a tooltip explaining why the button is disabled.
- Button opening a menu: `aria-haspopup="menu"` + `aria-expanded="true/false"`

### Keyboard

- `Enter` and `Space` activate (native `<button>` behavior)
- `Tab` moves focus in/out
- No additional JavaScript needed for basic buttons

### Structure

```tsx
<button
  ref={ref}
  className={cn(
    "rly-{component}",
    `rly-{component}--${type}`,
    loading && "rly-{component}--loading",
    disabled && "rly-{component}--disabled"
  )}
  disabled={disabled}
  aria-busy={loading || undefined}
  {...rest}
>
  {showLeftIcon && <span className="rly-{component}__icon" aria-hidden="true">{leftIcon}</span>}
  <span className="rly-{component}__label">{children}</span>
  {showRightIcon && <span className="rly-{component}__icon" aria-hidden="true">{rightIcon}</span>}
  {loading && <Spinner />}
</button>
```

### CSS pattern

Buttons don't need sibling selectors — pseudoclasses apply directly:

```css
.rly-{component}:hover:not(:disabled) { /* hover */ }
.rly-{component}:active:not(:disabled) { /* pressed */ }
.rly-{component}:focus-visible::after { /* focus ring */ }
.rly-{component}:disabled { opacity: 0.4; cursor: not-allowed; }
.rly-{component}--loading .rly-{component}__label,
.rly-{component}--loading .rly-{component}__icon { opacity: 0; }
```

---

## 6. Disclosure

**What it does:** Toggles visibility of related content. The trigger is always visible; the content shows/hides.

**Examples:** Accordion, collapsible section, expandable row, FAQ item, dropdown menu, popover, tooltip, "show more" truncation, navigation submenu, details/summary.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Simple expand/collapse | `<details>` + `<summary>` | Fully native, zero JS needed |
| Styled accordion | `<button aria-expanded>` + `<div role="region">` | Full control over styling and animation |
| Menu (list of actions) | `<button aria-haspopup="menu">` + `<ul role="menu">` | Menu semantics, roving tabindex |
| Tooltip | Trigger + `<div role="tooltip">` | Shows on hover/focus, informational only |
| Popover | Trigger + `<div>` with Popover API or absolute positioning | Rich content on click |

### ARIA

- Trigger: `aria-expanded="true/false"` — always present on the trigger element.
- Content: `aria-controls={contentId}` on the trigger, pointing to the expandable region.
- Accordion panel: `role="region"` + `aria-labelledby={triggerId}` on the content.
- Menu: `role="menu"` on the list, `role="menuitem"` on each item.
- Tooltip: `role="tooltip"` on the content, `aria-describedby={tooltipId}` on the trigger.

### Keyboard

- **Accordion/collapsible:** Enter/Space on the trigger toggles content. Tab moves between triggers.
- **Menu:** Enter/Space opens. Arrow Down/Up navigate items. Enter activates item. Escape closes. Tab closes and moves focus out.
- **Tooltip:** Shows on focus (keyboard) and hover (mouse). Escape dismisses. Not manually focusable — content is supplementary.

### Structure (accordion item)

```tsx
<div className="rly-{component}__item">
  <button
    className="rly-{component}__trigger"
    aria-expanded={isOpen}
    aria-controls={contentId}
    onClick={toggle}
  >
    <span>{title}</span>
    <ChevronIcon className={isOpen ? "rly-{component}__icon--open" : ""} />
  </button>
  <div
    id={contentId}
    role="region"
    aria-labelledby={triggerId}
    className="rly-{component}__content"
    hidden={!isOpen}
  >
    {children}
  </div>
</div>
```

### Structure (tooltip)

```tsx
<span
  className="rly-tooltip__trigger"
  aria-describedby={isVisible ? tooltipId : undefined}
  onMouseEnter={show}
  onMouseLeave={hide}
  onFocus={show}
  onBlur={hide}
>
  {children}
  {isVisible && (
    <span id={tooltipId} role="tooltip" className="rly-tooltip__content">
      {content}
    </span>
  )}
</span>
```

### Animation note

For expand/collapse animation, animate `max-height` or use `grid-template-rows: 0fr → 1fr` with `overflow: hidden`. Never animate `height: auto` directly (CSS can't interpolate to `auto`). The `hidden` attribute should be set after animation completes, or use `display: grid` approach to avoid it entirely.

---

## 7. Overlay

**What it does:** Content that floats above the page, usually blocking or dimming the background.

**Examples:** Modal/dialog, drawer/side panel, toast/snackbar, notification banner, bottom sheet, command palette, lightbox, confirmation dialog, fullscreen overlay.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Modal dialog | `<dialog>` | Native `showModal()`, focus trap, Escape close, backdrop |
| Non-modal dialog | `<dialog>` with `.show()` | No backdrop, no focus trap |
| Alert dialog | `<dialog role="alertdialog">` | Requires explicit user action to dismiss |
| Toast/notification | `<div role="alert">` or `<div role="status">` | Live region — announced by screen reader automatically |
| Drawer | `<dialog>` or `<aside>` with overlay behavior | Slide-in panel, same focus management as modal |

### ARIA

- Modal: `<dialog>` handles most ARIA automatically when using `showModal()`. Add `aria-labelledby` pointing to the title, `aria-describedby` pointing to the description.
- Alert dialog: `role="alertdialog"` — screen reader announces it immediately and urgently.
- Toast: `role="alert"` for urgent messages (errors), `role="status"` for non-urgent (success confirmations). Both are live regions — content is announced when it appears.
- Close button: `aria-label="Close"` if it's icon-only.

### Keyboard

- **Modal:** Escape closes. Tab cycles focus within the dialog (focus trap). Focus moves to the dialog on open. Focus returns to the trigger on close.
- **Toast:** Should not trap focus. May be dismissible with Escape. Should auto-dismiss after a timeout (provide enough time — minimum 5 seconds). **WCAG 2.2.1:** Pause the countdown on `mouseenter` and `focus`; resume on `mouseleave` and `blur`. Always provide a visible dismiss button so users are not forced to rely on the timer.
- **Command palette:** Escape closes. Arrow keys navigate results. Enter activates. Typing filters.

### Structure (modal dialog)

```tsx
const Dialog = forwardRef<HTMLDialogElement, DialogProps>(
  function Dialog({ title, children, onClose, ...rest }, ref) {
    const dialogRef = useRef<HTMLDialogElement>(null);
    const titleId = useId();

    // Merge refs, open/close via .showModal()/.close()

    return (
      <dialog
        ref={mergedRef}
        className="rly-dialog"
        aria-labelledby={titleId}
        onClose={onClose}
        {...rest}
      >
        <header className="rly-dialog__header">
          <h2 id={titleId}>{title}</h2>
          <button
            className="rly-dialog__close"
            aria-label="Close"
            onClick={() => dialogRef.current?.close()}
          >
            <CloseIcon />
          </button>
        </header>
        <div className="rly-dialog__body">
          {children}
        </div>
      </dialog>
    );
  }
);
```

### CSS pattern

```css
.rly-dialog::backdrop { background: var(--colors-component-bg-dialog); }
.rly-dialog {
  border: none;
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  padding: 0;
  max-width: min(90vw, 560px);
}
```

### Focus management

`<dialog>` with `showModal()` handles focus trapping natively in modern browsers. Return focus to the triggering element on close — store a reference to `document.activeElement` before opening, and call `.focus()` on it after closing.


### Scroll lock

Native `<dialog>` with `showModal()` blocks pointer interaction with elements behind the backdrop, but does **not** prevent body scroll on all browsers (notably Safari on iOS). When a modal is open, the page behind it must not scroll.

Add a `useEffect` that sets `overflow: hidden` on `<body>` while the overlay is open:

```tsx
useEffect(() => {
  if (!open) return;
  const prev = document.body.style.overflow;
  document.body.style.overflow = "hidden";
  return () => {
    document.body.style.overflow = prev;
  };
}, [open]);
```

This applies to all overlay-type components: modal dialog, drawer, bottom sheet, lightbox, command palette.

---

## 8. Display

**What it does:** Shows information visually. Not interactive — no click handlers, no keyboard focus.

**Examples:** Badge, avatar, tag/chip (read-only), progress bar, skeleton loader, divider, stat/metric, icon, loading spinner, empty state illustration, status dot, tooltip content.

### Which HTML element?

| Variant | Element | Why |
|---------|---------|-----|
| Inline indicator | `<span>` | Inline flow, no semantic meaning |
| Block container | `<div>` | Block-level display element |
| Progress | `<progress>` or `<div role="progressbar">` | Native progress semantics |
| Status message | `<div role="status">` | Live region for dynamic updates |
| Decorative | Any element + `aria-hidden="true"` | Hidden from screen readers |

### ARIA

- Decorative elements (visual-only dividers, background patterns, decorative icons): `aria-hidden="true"`
- Badges/tags next to text that already conveys the information: `aria-hidden="true"` (the text is sufficient)
- Badges/tags that carry standalone meaning: ensure the text content is descriptive
- Progress bar: `role="progressbar"`, `aria-valuenow`, `aria-valuemin`, `aria-valuemax`, `aria-label`
- Spinner/loader: `role="status"` + visually hidden text like "Loading..."
- Avatar with name: image `alt={name}` or `aria-label={name}`

### Keyboard

None. Display components are not focusable and not interactive. If a "display" component needs to be clickable (e.g., a clickable tag/chip), it becomes an Action Trigger — use a `<button>`.

### Structure (badge)

```tsx
<span
  className={cn("rly-badge", `rly-badge--${type}`, `rly-badge--${variant}`)}
  {...rest}
>
  {showIcon && <span className="rly-badge__icon" aria-hidden="true">{icon}</span>}
  <span className="rly-badge__label">{label}</span>
</span>
```

### Structure (progress bar)

```tsx
<div
  role="progressbar"
  aria-valuenow={value}
  aria-valuemin={0}
  aria-valuemax={max}
  aria-label={label}
  className="rly-progress"
>
  <div
    className="rly-progress__fill"
    style={{ width: `${(value / max) * 100}%` }}
  />
</div>
```

### Props pattern

Display components extend generic HTML attributes since they have no specific native element:

```tsx
interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement> {
  type?: "neutral" | "success" | "warning" | "error" | "info";
  variant?: "solid" | "subtle";
  label: string;
  showIcon?: boolean;
  icon?: React.ReactNode;
}
```

No forwardRef needed unless there's a concrete use case (e.g., positioning a tooltip relative to a badge).

---

## 9. Composite Components

Some components combine multiple archetypes. When you encounter one, read all the relevant archetype sections and merge their patterns.

### Common composites

| Component | Archetypes combined | Key challenge |
|-----------|-------------------|---------------|
| Dropdown / Select | Action Trigger + Disclosure + Single Selection | Focus management between trigger and list |
| Combobox / Autocomplete | Text Entry + Disclosure + Single Selection | Input drives filtering, arrow keys navigate options |
| Multi-select dropdown | Action Trigger + Disclosure + Multi Selection | Selected items displayed as chips/tags |
| Date picker | Action Trigger + Disclosure + Single Selection (calendar grid) | Grid navigation with Arrow keys |
| Color picker | Action Trigger + Disclosure + custom interaction | Pointer-driven area selection |
| Command palette | Overlay + Text Entry + Single Selection | Search + results navigation |
| Toast with action | Display + Action Trigger | Live region containing an interactive button |
| Chip/Tag (removable) | Display + Action Trigger | Display with embedded close button |
| Menu button | Action Trigger + Disclosure + list of Action Triggers | Roving tabindex in the menu items |
| Nav item with submenu | Action Trigger + Disclosure + list of Action Triggers | Hover and focus open submenu |
| Form field | Text Entry + Display (label, helper, error) | Wiring aria-describedby across sub-elements |

### How to build a composite

1. Identify which archetypes are involved
2. Read each archetype section
3. One archetype is primary (drives the root element and main interaction)
4. Other archetypes are secondary (provide sub-patterns within the component)
5. Wire ARIA relationships between parts: `aria-controls`, `aria-describedby`, `aria-labelledby`, `aria-activedescendant`
6. Manage focus flow: where does focus go when a disclosure opens? Where does it return when it closes?

### Example: Removable Tag

Primary archetype: **Display** (tag shows a value).
Secondary archetype: **Action Trigger** (close button removes it).

```tsx
<span className="rly-tag">
  <span className="rly-tag__label">{label}</span>
  {onRemove && (
    <button
      className="rly-tag__remove"
      aria-label={`Remove ${label}`}
      onClick={onRemove}
    >
      <CloseIcon />
    </button>
  )}
</span>
```

The tag itself is a `<span>` (Display), but the close button inside is a real `<button>` (Action Trigger) with its own `aria-label`, keyboard support, and focus management.
