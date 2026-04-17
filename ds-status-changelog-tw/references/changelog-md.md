# CHANGELOG.md — Structure & Rules

## Purpose
Chronological record of what changed, added, or removed.
Format inspired by keepachangelog.com — simplified for design systems.

## Full structure

```md
# Changelog

All notable changes to this design system are documented here.
Dates are in YYYY-MM-DD format. Newest entries first.

---

## Unreleased
*(changes not yet tagged/deployed)*

- [Button] Added Quaternary (ghost) variant
- [Tokens] Re-exported from Figma — added `--colors-bg-brand-secondary`

---

## 2026-03-25

### Added
- Button v2: new variant system (Primary/Secondary/Tertiary/Quaternary), sizes M and L
- Button v2: `startIcon`/`endIcon` props replace `leftIcon`/`rightIcon`

### Changed
- Button: `type` prop renamed to `variant` (breaking)
- Button: `state` prop removed — use CSS pseudo-classes (breaking)
- Button: Secondary is now filled light-gray, not outlined (breaking)

### Fixed
- Input: focus ring now escapes overflow correctly in Safari

### Deprecated
*(nothing)*

---

## 2026-03-24 — Initial export

### Added
- Tokens: full export from Figma Variables (colors, typography, spacing, radius, shadows)
- Components: Input, Badge, Checkbox, Radio, Switch, IconButton, LinkButton
- Showcase: index.html with all component variants
```

## Entry rules

**Categories** (use only what's needed per release):
- `Added` — new component or new prop/variant on existing component
- `Changed` — modified behavior or API (note if breaking)
- `Fixed` — bug fix
- `Deprecated` — still works but will be removed
- `Removed` — deleted

**Format per line:**
`- [ComponentName] What changed. (breaking)` if it's a breaking change.

**Breaking changes** — always mark with `(breaking)` and explain migration:
```
- [Button] `type` prop renamed to `variant` — replace all `type="Primary"` with `variant="Primary"` (breaking)
```

**Unreleased section:**
- Always present at top
- Items here don't need categories — flat list is fine
- When shipping/deploying, convert to dated section and re-add empty Unreleased

## What NOT to log
- Whitespace/formatting fixes
- Comment updates
- showcase-only changes that don't affect component API
- Token re-exports without API changes (log token additions, not the re-export itself)
