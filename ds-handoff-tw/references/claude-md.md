# CLAUDE.md — What to Update at Handoff

CLAUDE.md already exists in the repo — it was created by `ds-setup-tw` at project start.
Do NOT rewrite it from scratch. Read the existing file, then make targeted updates only.

## What to update

### 1. Component list pointer
CLAUDE.md should NOT contain a component status table — that belongs in STATUS.md.
Check: if the existing CLAUDE.md has a component table, verify it has a line like:
```md
## Component list
See `STATUS.md` for the full list of components and their status.
```
If the line is missing, add it and remove any inline component table.

### 2. Deploy URL
If a showcase was deployed (Vercel or similar), add or update the URL.
Find the existing line/section that mentions the showcase — update it in place.
If no such line exists, add it near the repo structure section:
```md
Showcase: https://[project].vercel.app
```

### 3. Last Figma export date
If there's a line like "Last export: YYYY-MM-DD", update it to today's date.

## What NOT to touch

- Do not rewrite sections that are still accurate
- Do not change the token rules, component conventions, or "What NOT to do" sections
  unless they are factually wrong
- Do not reformat or restructure the file — preserve the existing style

## Tone rules (for any text you do add)
- Imperative, not descriptive ("Use forwardRef" not "Components should use forwardRef")
- No "please", no "note that", no "it's important to"
- If something is forbidden, say it explicitly
