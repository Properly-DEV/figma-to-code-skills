---
name: ds-figma-templates-tw
description: "Use this skill when the user wants to extract a full page layout template from Figma — a structural wireframe showing grid, spacing, sections, and responsive behavior. Triggers: 'build this template', 'extract this layout', 'create a page template', 'document this page structure', providing a Figma link to a full page/screen. Do NOT use for individual components (use ds-figma-to-react-tw) or patterns (use ds-figma-patterns-tw)."
---

# Figma → Page Template

> **Language rule:** All generated files must be written in English.

Extract full page layouts from Figma into structural wireframe specs. Output serves two consumers: **developers** who need exact grid/flex rules and responsive behavior, and **AI agents** (Lovable, v0, Cursor) who need machine-readable page shells to generate working layouts.

**Principle:** Templates are structural blueprints — gray boxes showing proportions, spacing, and responsive behavior. No real content, no component logic.

---

## What a Template IS vs ISN'T

| Template IS | Template ISN'T |
|---|---|
| Structural wireframe with gray section boxes | A page with real data/content |
| Grid/flex rules, spacing, proportions | Component logic or styling |
| Responsive breakpoint behavior | A pattern (that's composition of 2-3 components) |
| Section names + what pattern goes where | A finished screen ready to ship |

---

## Decision tree — is this really a template?

```
Does the user point to:
├─ A single element with variants?           → Redirect to ds-figma-to-react-tw
├─ 2–3 components in a layout?              → Redirect to ds-figma-patterns-tw
├─ A full screen with component states?     → Redirect to ds-figma-patterns-tw
└─ A full page showing structural layout?   → This skill ✓
```

Templates focus on the **page shell** — where sections go, how they resize, what's fixed vs scrollable. If the user is focused on component composition or interaction states, redirect to patterns.

---

## Mode detection

Before starting, check if `templates/{TemplateName}.spec.md` already exists:
- **New** — file doesn't exist → create from scratch (normal flow)
- **Update** — file exists → read current spec, fetch new screenshot from Figma, compare what changed (new sections, layout shifts, changed breakpoints), and update the spec. Note what changed at the top of the file.

---

## Step 1 — Screenshot first

**ALWAYS start with `get_screenshot`.** Never open with `get_design_context`.

From the screenshot:
1. Identify major page sections (header, sidebar, content areas, footer, etc.)
2. Assess overall layout strategy (grid vs flex, fixed vs fluid)
3. Count distinct structural zones

> **Screenshot blind spots — do NOT infer these from a screenshot:**
> shadow (`box-shadow`), stroke/border on containers, `backdrop-filter`, `outline`.
> For these properties, the default is **none/0** unless confirmed by `get_design_context`.

---

## Step 2 — Get dimensions from Figma

Call `get_design_context` on **specific section nodes** (never the whole frame) to extract:
- Width, height constraints
- Padding, gaps
- Auto-layout direction and alignment
- Fixed/fluid behavior

---

## Step 3 — Interview the user

**MANDATORY.** Do not generate the template spec until these questions are answered.
For every question, offer: *"Answer 'AI fill' — I'll generate a reasonable default."*

### Essential questions (ask ALL):

1. **Grid system:** How many columns? Gutter width? Max content width? Or is it edge-to-edge?
2. **Responsive behavior:** What happens at tablet (~1024px)? Mobile (~768px)? Which sections collapse, hide, or stack?
3. **Fixed/sticky elements:** Which sections stay pinned? (header, sidebar, footer, toolbar)
4. **Scroll behavior:** Which sections scroll independently? Does the whole page scroll, or are there fixed panels with scrollable content?
5. **Section names:** Confirm naming for each area identified in the screenshot.
6. **Inner spacing:** Padding inside each section — same across all, or different per section?
7. **Outer spacing:** Gaps/margins between sections.
8. **Breakpoint-specific rules:** Any special behavior? (e.g., sidebar becomes bottom nav on mobile, columns collapse to accordion)

---

## Step 4 — Generate template spec

Save to `templates/{TemplateName}.spec.md`.

> **Value resolution — resolve BEFORE writing the spec:**
>
> For spacing/dimension values observed in Figma:
> 1. Match to existing Tailwind class via `tailwind.config.ts` → use that class (e.g. `gap-xl`, `p-2xl`, `h-16`).
> 2. No match → propose adding token to `tokens.css` + mapping in `tailwind.config.ts`, then use the new class.
> 3. Never use arbitrary values (`gap-[16px]`) when a mapped class exists.

Read `references/template-spec.md` for the exact format. The spec includes:
1. Page Anatomy — hierarchical list of all sections with layout rules
2. Grid & Layout Rules — table with width, height, position, overflow per section
3. Spacing — table of gaps between sections with token references
4. Responsive Breakpoints — table of what changes at each breakpoint
5. Section → Pattern/Component Map — what goes in each section, with build status
6. AI Agent Instructions — complete JSX layout shell with Tailwind classes

---

## Step 5 — Validate before delivering

- [ ] Every section from the screenshot is in the spec
- [ ] Layout rules cover: what is fixed, what scrolls, flex/grid direction, sizing
- [ ] Spacing values use design tokens (Tailwind classes), not raw px
- [ ] All responsive breakpoints are documented
- [ ] Section → Pattern/Component map links to existing components where available
- [ ] AI Agent Instructions include a complete JSX shell that compiles
- [ ] No `box-shadow`, `border`, or decorative styles — this is structure only

---

## Step 6 — Update STATUS.md

Add or update the template row in `## Templates` section of STATUS.md.
If STATUS.md doesn't have a Templates section, create one.

---

## Finish — TL;DR for user

After completing all steps, print a short summary:

Done: [what was created — file name]
Gaps: [what's missing, blocked, or needs user decision — or "none"]
Next: [suggested next action or skill to run]

Keep it to 3-5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.

---

## References

- **`references/template-spec.md`** — full spec.md template with all sections. Read in Step 4.
