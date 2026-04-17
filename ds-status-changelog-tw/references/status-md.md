# STATUS.md — Structure & Rules

## Purpose
Single source of truth for "what can I use right now?"
Updated manually — not generated automatically.
Lives at repo root. Linked from README.md.

## Full structure

```md
# Design System Status
Last updated: YYYY-MM-DD

## Legend
| Emoji | Meaning |
|-------|---------|
| ✅ | Done — safe to use in production |
| 🔧 | In Progress — API may change |
| 🔲 | Planned — Figma exists, not coded yet |
| ⚠️ | Needs Review — known issues |
| 🚫 | Deprecated — do not use |

## Components
| Component | Status | Figma | Variants | Notes |
|-----------|--------|-------|----------|-------|
| Button    | ✅     | [node](figma-url) | variant × size × loading | v2 — breaking from old |
| Input     | ✅     | [node](figma-url) | state × filled | |
| Badge     | ✅     | [node](figma-url) | type × variant × hasIcon | |
| Checkbox  | ✅     | [node](figma-url) | state × isChecked × showText | |
| Radio     | ✅     | [node](figma-url) | state × isChecked × showText | |
| Switch    | ✅     | [node](figma-url) | state × isSelected × isDisabled | |
| IconButton| ✅     | [node](figma-url) | type × state | |
| LinkButton| ✅     | [node](figma-url) | size × state | |

## Tokens
| Category | Status | Source |
|----------|--------|--------|
| Colors (base, neutral, brand, semantic) | ✅ | tokens.css |
| Typography | ✅ | tokens.css |
| Spacing | ✅ | tokens.css |
| Radius | ✅ | tokens.css |
| Shadows | ✅ | tokens.css |
| Icons | 🔲 | SVG set not yet extracted |

## Patterns
| Pattern | Status | Spec | HTML Preview |
|---------|--------|------|--------------|
| _(none yet)_ | — | — | — |

## Showcase
| Item | Status | URL |
|------|--------|-----|
| index.html (local) | ✅ | open index.html |
| Deployed showcase | 🔲 | — |

## Known issues
- [ ] #001 — [Component] description of issue

---

See `TODO.md` for items pending resolution before next handoff.
```

## Rules
- Update "Last updated" date every time you touch the file
- Variants column: use `×` not `x` (multiply sign, not letter)
- Notes: only add if there's something a dev needs to know before using
- Keep it scannable — no paragraphs
- Patterns section: add a row when ds-figma-patterns-tw produces a new pattern. Status `🔲 Planned` items should map to corresponding TODO.md entries.
