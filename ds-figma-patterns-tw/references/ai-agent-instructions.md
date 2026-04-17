# AI Agent Instructions Guide

**When to read:** During Step 4, when writing the `AI Agent Instructions` section at the end of spec.md. Always generated for both Path A and Path B.

**Purpose:** AI agents (Lovable, Cursor, Bolt) and developers read this section to understand what to build, what Tailwind classes to use, and what to leave as TODOs. Write as direct imperatives, not descriptions.

---

## Format for the AI Agent Instructions section

Place as the **last section** in every spec.md:

```markdown
---

## AI Agent Instructions

You are implementing **{PatternName}**. This is a visual skeleton — implement structure and styles first. Add application logic (API calls, state management, routing) as TODO comments.

### Stack
- React + TypeScript + Tailwind CSS
- Use `cn()` from `lib/utils` for conditional class merging
- Use `cva` from `class-variance-authority` only if this pattern has variant dimensions
- Import components from /components/ — do not recreate them

### Components
- `{ComponentA}` — use for {role in pattern}
- `{ComponentB}` — use for {role in pattern}
- `{ComponentC}` — use for {role in pattern}

### Structure
Implement the component tree from the Anatomy section exactly — preserve nesting, order, and layout.

### Layout (Tailwind classes)
- Container: `flex flex-col gap-4 p-6`
- {SectionA}: `sticky top-0 z-10 flex items-center justify-between p-4`
- {SectionB}: `flex-1 overflow-y-auto flex flex-col gap-2 p-4`
- {SectionC}: `sticky bottom-0 flex items-center gap-2 p-4 border-t border-tertiary`

Use only Tailwind classes mapped to design tokens in tailwind.config.ts. Never use arbitrary values (`bg-[#hex]`, `p-[16px]`) when a named class exists. Never use `style={{}}` for static values.

### States
Default render: **Loaded** state.
- Empty: {one-line description}
- Loading: {one-line description — skeleton elements with `animate-pulse bg-tertiary rounded-sm`}
- Error: {one-line description}

### Behaviour
- After {action}: {immediate visual change}
- Scroll to bottom when new item appended: `scrollIntoView({ behavior: 'smooth' })`
- {ComponentX} gets `className="opacity-60"` in sending state

### TODOs (mark as comments, do not implement)
- Data fetching / API calls
- WebSocket or real-time connections
- Routing and navigation
- Authentication / permissions

### Notes
{Pattern-specific callouts, e.g. "Responsive not specified — assume single column on mobile"}
```

---

## Writing good vs bad instructions

**Good — imperative, specific, uses Tailwind classes:**
```markdown
- Import `ChatBubble` from /components/ChatBubble.tsx — do not recreate.
- Message list container: `flex-1 overflow-y-auto flex flex-col gap-2 p-4`
- ChatBubble in sending state: add `className="opacity-60"` via cn()
- Scroll to bottom after appending: `ref.current?.scrollIntoView({ behavior: 'smooth' })`
- Leave API call as `// TODO: fetch messages from API`
```

**Bad — vague, no Tailwind specifics:**
```markdown
- Use the ChatBubble component
- Messages should be scrollable with some padding
- Sending messages look different
- WebSocket will be needed later
```

---

## How AI agents read this file

Agents prioritise:
1. **Stack declaration** — they configure their output (React + Tailwind, not Vue + CSS Modules)
2. **Component imports** — explicit "import X from Y" instructions
3. **Layout classes** — they apply these verbatim to wrapper elements
4. **Explicit constraints** — "never arbitrary values", "do not recreate"
5. **TODOs** — they leave as comments, not implement

Keep the section **dense and imperative**. No full sentences where a bullet suffices. No explanations of why — just what.

---

## When sections have "AI fill" defaults

```markdown
> **Auto-generated defaults applied** — sections marked with `<!-- AI-generated default -->` were not specified by the designer. Review before shipping:
> - Responsive: single column mobile, as-designed desktop
> - Animations: none assumed
> - Data contract: inferred from visible elements
```

---

## Universal rules

**Interactive element nesting:** `<button>` inside `<label>`, `<a>` inside `<button>`, or `<button>` inside `<a>` = invalid HTML. Check what parent component renders before nesting interactive children.

**No inline styles:** If a component needs a layout adjustment, use a Tailwind class on its wrapper — never `style={{ width: "100%" }}`. Check if the component has a prop for it first (e.g. `className="w-full"`).

---

## Path A — minimal AI Agent Instructions

```markdown
## AI Agent Instructions

Implement **{PatternName}** with React + TypeScript + Tailwind.

### Components
- `{ComponentA}` — [existing: /components/{ComponentA}.tsx]
- `{ComponentB}` — [existing: /components/{ComponentB}.tsx]

### Layout
Container: `flex flex-col gap-4 p-6` (adjust gap/padding per spec).
Use only Tailwind classes from tailwind.config.ts — no arbitrary values.
No application logic needed for this composite.
```
