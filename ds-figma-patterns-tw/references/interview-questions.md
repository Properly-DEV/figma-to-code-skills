# Interview Questions

**When to read:** Always, during Step 3 of the skill. Use this bank to select relevant questions based on path (A or B) and pattern type.

**Rule:** Never ask all questions at once. Start with the essentials (marked ★). Ask follow-ups only if the answer opens a gap.

**Escape hatch:** For every question, tell the user: *"Answer 'AI fill' to skip — I'll generate a reasonable default."*

---

## ★ Always ask (both Path A and B)

### View states
> "What states can this view be in?"

Common states to prompt for if the user doesn't mention them:
- **Empty** — no content yet (new user, no data)
- **Loading** — data fetching in progress
- **Loaded** — default, content visible
- **Error** — something went wrong
- **Partial** — some content loaded, some still loading

> "Do you have all these states designed in Figma, or do some need to be described?"

---

## Path B only

### Interactions (Section C)
> "What happens after the main user action — step by step?"

Example prompt: *"When the user clicks [primary button], what changes visually? In what order?"*

> "Is there anything that animates, transitions, or appears conditionally?"
If skipped → generate: "No animations assumed. Add transitions as needed."

> "What happens on success? On error?"

### Scrolling & sticky behaviour (Section D)
> "What parts of this view scroll? What stays fixed?"

Common patterns to suggest:
- Header sticky top
- Footer / input sticky bottom
- Main content scrollable
- Scroll-to-bottom on new content

> "Does anything auto-scroll? If yes, when and to where?"
If skipped → generate: "No auto-scroll assumed."

### Responsive behaviour (Section D)
> "How does this look on mobile (< 768px)? Same layout or different?"
If skipped → generate: "Mobile: full-width, single column. Desktop: max-width as in Figma."

> "Any breakpoints that change the layout significantly?"
If skipped → generate: "One breakpoint at 768px assumed."

### Animations & transitions
> "Does anything animate in/out? (e.g. new message slides in, modal fades in)"
If skipped → generate: "No animations assumed. Add `transition` as needed."

> "Are there any loading skeletons, spinners, or shimmer effects?"
If skipped → generate: "Use opacity pulse animation for loading states."

### Data contract (Section E)
> "What information does each repeating item need? (e.g. a message: text, sender, timestamp, status)"

> "Where does this data come from? (API call, local state, parent component, props)"
If skipped → generate: "Data source not specified. Accept as props and document as TODO."

> "Are there any real-time updates? (e.g. WebSocket, polling)"
If skipped → generate: "No real-time assumed. Mark as TODO in AI Agent Instructions."

---

## Optional — ask only if relevant

### Conditional visibility
> "Are there any elements that show/hide based on conditions?"
Example: "TypingIndicator only visible when the other user is typing"

### User-generated content edge cases
> "What happens with very long text? Very short text? Empty fields?"
If skipped → generate: "Truncate long text with ellipsis. Handle empty states with placeholder."

### Permissions or role-based visibility
> "Does any part of this view look different for different user roles?"
If skipped → generate: "No role-based differences assumed."

---

## After the interview — what to do with "AI fill" answers

For each skipped question:
1. Generate a minimal, sensible default
2. Mark the section in spec.md with a comment:
   ```
   <!-- AI-generated default — review before shipping -->
   ```
3. In the AI Agent Instructions section, flag it explicitly:
   ```
   NOTE: Responsive behaviour was not specified — default breakpoint at 768px applied. Adjust as needed.
   ```
