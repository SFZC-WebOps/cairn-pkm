# !qn — Quick Note

**Type:** Write | **Version:** 3.1

Quickly capture ideas and findings from conversation to the inbox.

---

## Quick Reference

| Command | What Happens | Permission |
|---------|--------------|------------|
| `!qn` | Capture from conversation | Confirmation required |
| `!qn [topic]` | Capture specific topic | Confirmation required |

---

## Workflow

1. Type `!qn` or `!qn [topic]`
2. Review proposed content
3. Confirm to create
4. File created in `Capture/`

---

## What It Extracts

From current conversation:
- Main topic discussed
- Key decisions or findings
- Action items (if any)
- Related project/task references

---

## Output Format

### Proposed File

```markdown
# Quick Note: {Topic}
Created: YYYY-MM-DD HH:MM:SS

## Context
What prompted this note

## Content
Main content from discussion

## Action Items (if any)
- [ ] Item 1
- [ ] Item 2

## References
- Related track: project/area if applicable
- Related task: task reference if applicable
```

### Filename

`qn-YYYY-MM-DD-HHMMSS-{slug}.md`

**Slug rules:**
- Max 3-4 words
- Lowercase, hyphens
- Descriptive of content

**Examples:**
- `qn-2025-09-26-143022-ssl-cert-fix.md`
- `qn-2025-09-26-151545-meeting-notes.md`
- `qn-2025-09-26-160130-deployment-steps.md`

---

## Processing Captures

During weekly review, each capture either:
- Becomes a **task** → moves to `Tracks/*/tasks/`
- Becomes **reference** → moves to `Objects/` or `Tracks/*/resources/`
- Gets **deleted** → wasn't worth keeping

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation empty | "No context to capture. What should I note?" |
| Write fails | "Failed to create: {error}" — Offer retry |

---

*Back to [Commands Index](index.md)*
