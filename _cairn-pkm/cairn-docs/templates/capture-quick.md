# capture-quick Template

Quick capture for inbox items.

---

## Prompts

| Prompt | Purpose |
|--------|---------|
| Subject | Brief description of what this is about |

---

## Output

**Filename:** `YYYYMMDD-HHMMSS-slug.md`

**Location:** `Capture/`

---

## Generated File

```markdown
---
title: "{subject}"
captured: YYYY-MM-DD HH:mm
type: capture
status: unprocessed
tags:
  - inbox
---

# {subject}

## Notes


## Next Actions
- 

## Process To
<!-- Where should this go? -->
<!-- Track: Tracks/area-xxx or Tracks/p###-xxxx-yyyy -->
<!-- Object: Objects/ -->
<!-- Delete: Not worth keeping -->
```

---

## Processing

During review, each capture either:
- Becomes a **task** → `Tracks/*/tasks/`
- Becomes **reference** → `Objects/` or `Tracks/*/resources/`
- Gets **deleted**

---

*Back to [Templates Index](index.md)*
