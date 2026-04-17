---
name: ds-figma-patterns-tw
description: "Use this skill when the user wants to extract a pattern, view, or screen from Figma — a composition of multiple components (e.g. chat view, registration form, dashboard section, onboarding screen). Triggers: 'build this pattern', 'extract this view', 'document this screen', 'create a spec for this layout', 'export this screen', providing a Figma link to a full screen or multi-component frame. Do NOT use for single components with variants — use ds-figma-to-react-tw for those."
---

# Figma → Pattern Spec

> **Language rule:** All generated files must be written in English.

Extract Figma patterns into a visual skeleton + behaviour spec. Output serves two consumers: **developers** who need a structural starting point with Tailwind classes, and **AI agents** (Lovable, Cursor, Bolt) who need machine-readable instructions to generate working React + Tailwind code.

**Principle:** Get the visual structure perfectly right first. States, interactions, and data details can be refined later.

---

## Decision tree — what are we building?

```
Does the user point to:
├─ A single element with variants?      → Redirect to ds-figma-to-react-tw
├─ 2–3 known components in a layout?   → Path A: Simple Composite
└─ A full screen / view with states?   → Path B: Full Pattern
```

If unsure, ask: *"Does this have multiple view states (loading, empty, error)?"* — Yes → Path B.

---

## Mode detection

Before starting, check if `patterns/{PatternName}.spec.md` already exists:
- **New** — file doesn't exist → create from scratch (normal flow)
- **Update** — file exists → read current spec + HTML, fetch new screenshot from Figma, compare what changed (new components, layout shifts, added states), and update both files. Add a CHANGELOG entry.

---

## Step 1 — Screenshot first

**ALWAYS start with `get_screenshot`.** Never open with `get_design_context`.

From the screenshot:
1. Identify which existing `.tsx` components from `/components/` are visible
2. Assess the layout complexity
3. Decide: Path A or Path B?

> **Screenshot blind spots — do NOT infer these from a screenshot:**
> shadow (`box-shadow`), stroke/border on containers, `backdrop-filter`, `outline`.
> For these properties, the default is **none/0** unless confirmed by `get_design_context`
> on the container node or visible in an existing `.tsx` component file.

**Path A (Simple Composite):** All elements are known components + basic layout → screenshot is enough. Read `references/simple-composite.md`.

**Path B (Full Pattern):** New elements, complex layout, or multiple states needed → may need `get_design_context` on **specific sub-nodes only** (never on the whole frame). Read `references/full-pattern.md`.

---

## Step 2 — Component inventory

List every visible component in the pattern. Check `/components/` for each one.

- Exists as `.tsx` → import it, don't rebuild
- Missing + simple (Divider, Timestamp) → build inline
- Missing + complex → tell user: *"Run ds-figma-to-react-tw on [X] first, then come back."*

> **Tool rule:** Read component files directly — do NOT spawn an Explore agent. Direct Read on 3–5 files costs ~8k tokens vs ~50k for Explore.

---

## Step 3 — Interview the user

**For every question offer an escape hatch:** *"Answer 'AI fill' — I'll generate a reasonable default."*

**Path A — ask only this:**
> "What states can this composite be in? (loaded only, or also empty/error?)"
If loaded only → skip interview, go to Step 4.

**Path B — read `references/interview-questions.md`** for the full question bank. Start with ★ essential questions only.

---

## Step 4 — Generate output

Generate two files simultaneously and save to `/patterns/`:

### Path A — read `references/simple-composite.md`

### Path B — read these before writing:
- `references/full-pattern.md` → full spec.md template
- `references/html-prototype-guide.md` → HTML with state switcher
- `references/ai-agent-instructions.md` → how to write the AI Agent Instructions section

> **Value resolution — resolve BEFORE writing any HTML or spec:**
>
> **For numeric values** (padding, gap, border-radius observed in design):
> 1. Match to existing Tailwind class via `tailwind.config.ts` → use that class (e.g. `gap-4`, `p-6`, `rounded-md`).
> 2. No match → propose adding token to `tokens.css` + mapping in `tailwind.config.ts`, then use the new class.
> 3. Never use arbitrary values (`gap-[16px]`) when a mapped class exists.
>
> **For component layout properties** (full-width button, fixed-height input, icon size):
> 1. Check the component's `.tsx` file for a prop that expresses this — e.g. `size="lg"`, `className="w-full"`.
> 2. If the prop exists → use it.
> 3. If no prop → use a Tailwind utility class on the wrapper, never `style={{}}`.

### Both paths produce two files — do not finish until both exist on disk:
1. **`{PatternName}.html`** — visual prototype using `tokens.css` CSS variables, hardcoded sample data
2. **`{PatternName}.spec.md`** — structured spec ending with **AI Agent Instructions** using Tailwind classes

The HTML file is not optional. If you run out of detail, write a minimal skeleton — but always produce it.

> **Why the HTML prototype uses CSS variables, not Tailwind classes:** The prototype opens in a plain browser with zero build tools. Tailwind CDN doesn't know the project's custom `tailwind.config.ts` mappings (like `bg-brand-primary`). CSS variables from `tokens.css` work anywhere. The spec.md / AI Agent Instructions contain the Tailwind class equivalents for production code.

---

## Step 5 — Validate before delivering

- [ ] Every component in Anatomy has a source: existing `.tsx` OR inline element
- [ ] Every view state from the interview has a corresponding HTML tab
- [ ] HTML prototype uses only `tokens.css` variables — zero hardcoded hex/px values
- [ ] spec.md AI Agent Instructions uses Tailwind class names for layout values
- [ ] `AI Agent Instructions` section exists in spec.md
- [ ] Layout rules cover: what is sticky, what scrolls, key spacing values as Tailwind classes
- [ ] No `box-shadow`, `border`, or `stroke` added to containers without Figma confirmation
- [ ] Interactive element nesting: no `<button>`, `<a>`, or `<input>` nested inside another interactive element
- [ ] HTML is functional: checkboxes toggle, inputs accept text, forms submit on Enter
- [ ] Every `<input>` has a `<label>` with matching `for`/`id`
- [ ] Buttons inside `<form>` have correct `type`: submit for main action, button for everything else
- [ ] Interactive elements without visible text have `aria-label` (icon buttons, clear buttons)
- [ ] AI Agent Instructions include: imports, component usage with exact props, and behaviour (what happens on submit, toggle, click)

## Step 6 — Deliver audit file and update STATUS.md

### pattern-audit.md

Save `patterns/{PatternName}-audit.md` — two sections only:

```markdown
# {PatternName} — Pattern Audit

## Figma Translation
| Figma element | Code equivalent |
|---------------|-----------------|
| [layer name]  | [component / Tailwind classes] |

## Token Usage
| Tailwind class | CSS variable | Used for |
|----------------|-------------|----------|
| bg-brand-primary | var(--colors-bg-brand-primary) | CTA button background |
| gap-4 | var(--spacing-4) | section spacing |
| rounded-md | var(--radius-md) | card corners |
```

### STATUS.md update

Update `STATUS.md` — add or update the pattern row in `## Patterns`.
If STATUS.md does not exist, skip and note it to the user.

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

- **`references/simple-composite.md`** — Path A template. Read in Step 4.
- **`references/full-pattern.md`** — Path B spec template. Read in Step 4.
- **`references/html-prototype-guide.md`** — HTML prototype with state switcher. Read in Step 4 (Path B).
- **`references/ai-agent-instructions.md`** — How to write AI Agent Instructions. Read in Step 4.
- **`references/interview-questions.md`** — Question bank for Step 3.
