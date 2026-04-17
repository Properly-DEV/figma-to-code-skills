---
name: ds-qa-tw
description: "Audit and refactor React + Tailwind design system components to production quality. Triggers: 'audit this component', 'is this production ready', 'review my Button/Input/etc', 'refactor this to best practices', 'QA this component', 'check component quality'. Also triggers when the user shares a component file and asks what's wrong with it."
---

# Design System QA — React + Tailwind

Audit and refactor React + Tailwind components to production quality.

> **Language rule:** All generated files (audits, indexes, reports) must be written in English.

## Two Modes

| Mode | Trigger | What happens |
|------|---------|--------------|
| **Audit** | "audit", "review", "QA", "check", "is this ready" | Profile → Evaluate → Report. No code changes. |
| **Refactor** | "refactor", "fix", "improve", "make production ready" | Audit first, then fix issues one by one. |

Default to Audit if ambiguous.

---

## Step 0: Read Project Context

Read `CLAUDE.md` in the project root. Extract:
- Token paths (`tokens/tokens.css`, `tailwind.config.ts`)
- `cn()` import path
- Naming convention (variant/size values)
- Any project-specific notes

If CLAUDE.md is absent: read `tailwind.config.ts` and 1–2 existing components to understand the project's conventions. If neither exists, evaluate against universal rules only.

Then read the component under audit.

---

## Step 1: Audit

Create `_designer/audits/` if it doesn't exist. Check if `_designer/audits/{ComponentName}.audit.md` exists. If it does — read it. You now have history: previous findings, what was fixed, what remains open. If this will be v2+, you must run a regression check at the end of this step against the previous version's findings.

Read `references/audit-rules.md`. Five dimensions:

1. **Props API** — Idiomatic React? Extends native HTML attrs?
2. **Styles & Tokens** — All values from Tailwind token classes? No hardcoded hex/px?
3. **Interaction States** — Tailwind modifiers, not JS state?
4. **Accessibility** — Native elements? Keyboard? Screen reader?
5. **Architecture** — File structure? No layout opinions?

Classify each finding by severity (read `references/severity-guide.md`):

| Severity | Meaning |
|----------|---------|
| **Critical** | Broken for end users or developers |
| **Warning** | Works but not production-grade |
| **Suggestion** | Polish and optimization |

### Audit Report Format

Findings grouped by severity, highest first:

```
### [Severity] Short description
**Rule:** Which rule from which dimension
**Location:** File + line/area
**Problem:** What's wrong and why it matters
**Fix:** Concrete before → after code
```

End with summary: X critical, Y warnings, Z suggestions.

### Systemic Findings

If a finding applies to ALL components (not just this one), tag it as **Systemic**. Check `../ds-figma-to-react-tw/references/anti-patterns.md`:
- Already documented → status **"Confirmed ✓"**
- Not documented → status **"→ PROMOTE"** — propose adding to anti-patterns.md. Do NOT add yourself — wait for user approval.

---

## Step 2: Refactor (only in Refactor mode)

Read `references/refactor-playbook.md`. Key principle: **one change at a time, verify after each.**

1. Fix **Critical** — native elements, props API, forwardRef
2. Fix **Warning** — token usage, interaction states, cn() usage
3. Apply **Suggestions** — naming, transitions, file order

After refactoring, re-run the audit mentally. Report should come back clean.

### Breaking Changes

If the refactor changes the props API, list every breaking change with migration:

```tsx
// Before
<Button type="Primary" state="Disabled" />

// After
<Button variant="primary" disabled />
```

---

## Step 2.5: Record

**Update the audit file every time something changes** — after the initial audit, after each fix, after a refactor. The audit file is the single source of truth for the component's quality status.

1. **Audit file** — create or append to `_designer/audits/{ComponentName}.audit.md`:

```markdown
## v{N} — {YYYY-MM-DD} | {audit|refactor|fix}
| # | Severity | Finding | Status |
|---|----------|---------|--------|
| 1 | Critical | {description} | Fixed v{N} |
| 2 | Warning  | {description} | Open |
| 3 | Token gap | {value} — no token | → TODO.md |
| 4 | Systemic | {description} | Confirmed ✓ / → PROMOTE |

{If v2+:} Regression check against v{N-1}: findings #{list} still clean / regressed.

### Status
Open: {count} | Fixed: {count} | Accepted: {count}
```

**Status rules:**
- When a finding is fixed → update its Status cell to `Fixed v{N}` with a short note of what was done
- When a finding is accepted (not a real issue) → `Accepted — {reason}`
- Always end the audit file with a `### Status` summary line so anyone opening it sees the current state instantly
- If fixes happen outside of a formal audit/refactor (e.g. after user feedback), append a new version entry with `| fix` tag

2. **Token gaps → TODO.md** — for each gap, append:
```
- [ ] Token gap ({ComponentName}): {value} — add {token name} to tokens.css + {mapping} to tailwind.config.ts
```

3. **Token gap comments in code** — at each arbitrary value with no token:
```tsx
className="p-[40px]" // TOKEN GAP: see TODO.md
```

4. **PROMOTE approved** — if the user approved a → PROMOTE finding, append a new numbered section with ❌/✅ example to `../ds-figma-to-react-tw/references/anti-patterns.md`.

5. **Designer audit index** — append a one-line entry to `_designer/audits.md` after every audit or refactor. This file is an **index only** — details stay in per-component files (`_designer/audits/{ComponentName}.audit.md`).

Format per entry (one line, in English):

```
- {YYYY-MM-DD} | **{ComponentName}** | {audit|refactor} | {X} Critical, {Y} Warning, {Z} Suggestion | {N} fixed, {M} open | [details](audits/{ComponentName}.audit.md)
```

Rules:
- Append after EVERY pass of this skill (audit and refactor)
- If audit → then refactor in the same session: one entry with final counts after refactor
- If `_designer/audits.md` does not exist, create it with header: `# Design System Audits\n\nAudit summary. Details in per-component files in `_designer/audits/`.`
- Never duplicate finding tables here — that belongs only in the per-component file

### Token gap rule

NEVER guess the "closest token". If Figma says `40px` and `tokens.css` has no `--spacing-10` — do NOT substitute `--spacing-8` (32px). Report the gap, use the arbitrary value with `// TOKEN GAP`, add to TODO.md. The human decides: add a token, change the design, or accept the exception.

---

## Step 3: Validate

Run this checklist after audit or refactor. Do not print it — if something fails, fix it (refactor) or add it to the report (audit).

1. **Keyboard:** Tab focuses. Space/Enter activates or toggles.
2. **Interaction states:** `hover:`, `focus-visible:`, `active:`, `disabled:` modifiers. No JS state for these.
3. **Disabled:** Native `disabled` blocks interaction. `disabled:opacity-40`.
4. **Screen reader:** Role, state, label announced correctly. Native elements used.
5. **Form integration:** Works with React Hook Form. Controlled and uncontrolled modes.
6. **forwardRef:** Ref reaches the native element.
7. **Tokens only:** Every visual class resolves to a design token via tailwind.config.ts. No `[#hex]`, no `[14px]`.
8. **Pure Tailwind:** No `.css` files, no `<style>` tags, no `style={{}}` for static values.
9. **cn() on root:** `className` prop merged via `cn()` — consumer can override/extend.
10. **Icons:** `currentColor` + `aria-hidden="true"`.
11. **No layout opinion:** No fixed width or margin on root.
12. **Defensive:** Handles `undefined` props, empty labels, unexpected variant values.

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

- **`references/audit-rules.md`** — All five dimensions with rules, ❌/✅ examples. Read in Step 1.
- **`references/severity-guide.md`** — How to classify findings. Read in Step 1.
- **`references/refactor-playbook.md`** — Safe refactoring process. Read in Step 2.
