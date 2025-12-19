# track-area Template

Create a complete area folder structure.

---

## Prompts

| Prompt | Purpose | Example |
|--------|---------|---------|
| Area name | Name of the area | Finance, Health, WebOps |

---

## Output

**Folder:** `Tracks/area-{slug}/`

---

## Created Structure

```
Tracks/area-{slug}/
├── _area-{slug}-home.md
├── resources/
├── tasks/
└── zzz/
```

---

## Generated Home Doc

```markdown
---
title: "{title}"
type: area
status: active
created: YYYY-MM-DD
modified: YYYY-MM-DD
tags:
  - area
---

## Overview

[What this area covers and why it matters]

## Current Focus

[What's active right now in this area]

## Standards

- [Standard to maintain]
- [Standard to maintain]

## Notes

[Working scratchpad — open questions, things to remember]

---

## Log

---

YYYY-MM-DD - Created area


```

---

## Areas vs. Projects

| Aspect | Areas | Projects |
|--------|-------|----------|
| Duration | Ongoing, no end | Temporary, has end |
| Pattern | `area-{domain}` | `p###-xxxx-yyyy` |
| When done | Never (archive if life changes) | Archive or delete |

---

## Common Areas

| Area | Purpose |
|------|---------|
| area-personal | Personal life management |
| area-finance | Financial management |
| area-health | Health and fitness |
| area-work | Professional/career |
| area-home | Home and household |
| area-admin | Administrative tasks |
| area-webops | Web operations |

---

*Back to [Templates Index](index.md)*
