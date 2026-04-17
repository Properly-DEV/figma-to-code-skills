# Full Pattern — Path B

**When to read:** The pattern is a full screen or complex view with multiple states, new elements, or non-trivial layout.

---

## Getting design context efficiently

**Never call `get_design_context` on the whole frame.** It consumes ~25k tokens and returns noise.

Strategy:
1. Start with `get_screenshot` of the full frame — identify all sections
2. For components already in `/components/` — skip context, screenshot only
3. Call `get_design_context` only on **unknown or complex sub-nodes**
4. **Always call `get_design_context` on the outermost non-component container** — the card, panel, or wrapper div. One call (~2–4k tokens) confirms shadow, border, exact padding, and border-radius.

If the user has multiple state frames in Figma (e.g. `chat-empty`, `chat-loaded`, `chat-error`):
- `get_screenshot` of each state frame
- `get_design_context` only if a state introduces new/unknown elements

---

## Full spec.md template (Path B)

```markdown
# {PatternName} — Pattern Spec

## A — Anatomy

{PatternName}
├── {SectionA}                    [sticky top | scrollable | fixed height]
│   ├── {ComponentA}              [existing: /components/{ComponentA}.tsx]
│   ├── {ComponentB}              [existing: /components/{ComponentB}.tsx]
│   └── {ElementC}                [inline: span, text-secondary text-body-4]
├── {SectionB}                    [flex-grow, scrollable]
│   └── {ComponentD}[]            [existing: /components/{ComponentD}.tsx, repeating]
└── {SectionC}                    [sticky bottom]
    ├── {ComponentE}              [existing: /components/{ComponentE}.tsx]
    └── {ComponentF}              [existing: /components/{ComponentF}.tsx]

## B — States

| State    | Description                  | What is visible              | Figma frame      |
|----------|------------------------------|------------------------------|------------------|
| Empty    | No content yet               | CTA / empty state message    | {frame-name}     |
| Loading  | Data fetching in progress    | Skeleton placeholders        | {frame-name}     |
| Loaded   | Content available            | Full component tree          | {frame-name}     |
| Error    | Load failed                  | Error message + retry action | {frame-name}     |

## C — Interactions

[AI fill OK if user skips]

→ User performs {main action}:
  1. {Visual change #1}
  2. {Visual change #2}
  3. On success: {outcome}
     On error: {outcome}

## D — Layout (Tailwind classes)

Container:
  `flex flex-col h-screen` (or appropriate height constraint)

| Section | Tailwind classes | Behaviour |
|---------|-----------------|-----------|
| {SectionA} | `sticky top-0 z-10 flex items-center justify-between p-4 border-b border-tertiary` | Sticky header |
| {SectionB} | `flex-1 overflow-y-auto flex flex-col gap-2 p-4` | Scrollable content |
| {SectionC} | `sticky bottom-0 flex items-center gap-2 p-4 border-t border-tertiary` | Sticky footer |

Breakpoints: [AI fill OK]
- < 768px → single column, full width
- ≥ 768px → as designed

## E — Data Contract

[AI fill OK if user skips]

{PrimaryEntity}:
  - id: string
  - {field}: {type} — {description}

{ViewProps}:
  - {field}: {type}
  - isLoading: boolean
  - error: string | null

## F — Form State [include only if pattern contains a form]

### Library
- **useState** — simple forms: ≤4 fields, no async validation
- **react-hook-form + Zod** — complex forms: 5+ fields OR async validation

### Zod schema [if react-hook-form]
\`\`\`ts
const {schemaName}Schema = z.object({
  {field}: z.string().min(1, "Required"),
  {field}: z.string().email("Invalid email"),
});
type {SchemaName}Values = z.infer<typeof {schemaName}Schema>;
\`\`\`

### Validation trigger
- On submit only (safe default)
- On blur after first submit attempt
- Real-time (only for specific fields)

### Success state
- Navigation: {route} OR inline success message
- Visual: {toast | redirect | replace form}
```

After completing sections A–F, write the **AI Agent Instructions** section.
Read `references/ai-agent-instructions.md` for format and examples.

---

## Notes on "AI fill OK" fields

When the user skips a question:
1. Generate a minimal, sensible default
2. Mark with: `<!-- AI-generated default — review before shipping -->`
3. Flag in AI Agent Instructions:
   ```
   NOTE: Responsive behaviour not specified — single column mobile assumed.
   ```
