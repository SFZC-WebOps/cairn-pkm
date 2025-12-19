# track-project Template

Create a complete project folder structure.

---

## Prompts

| Prompt | Purpose | Example |
|--------|---------|---------|
| Project name | Full name of the project | "Blog Migration" |
| System code | 4-letter system identifier | blog, home, work |
| Action code | 4-letter action identifier | migr, setup, audt |

---

## Output

**Folder:** `Tracks/p###-xxxx-yyyy/`

The template automatically finds the next available project number.

---

## Created Structure

```
Tracks/p###-xxxx-yyyy/
├── _p###-xxxx-yyyy-home.md
├── resources/
├── tasks/
└── zzz/
```

---

## Generated Home Doc

```markdown
---
project_id: p###-xxxx-yyyy
title: "{title}"
type: project
status: active
progress: 0
created: YYYY-MM-DD
modified: YYYY-MM-DD
tags:
  - project
---

## Overview

[What this project accomplishes and why]

## Success Criteria

- [ ] [Measurable outcome]
- [ ] [Measurable outcome]
- [ ] [Measurable outcome]

## Current Focus

[What's active right now]

## Notes

[Working scratchpad — open questions, pending decisions]

---

## Log

---

YYYY-MM-DD - Created project


```

---

## Folder Purposes

| Folder | Purpose |
|--------|---------|
| `resources/` | Supporting documents, references |
| `tasks/` | Task files for this project |
| `zzz/` | Archive for completed/obsolete items |

---

## Project ID System

Format: `p` + `###` + `-` + `xxxx` + `-` + `yyyy`

- `p` = project prefix
- `###` = sequential number (001-999, zero-padded)
- `xxxx` = system code (4 letters, readable)
- `yyyy` = action code (4 letters, readable)

### Common System Codes

| Code | Meaning |
|------|---------|
| blog | Blog/content |
| home | Home/personal |
| work | Work/professional |
| hlth | Health |
| finc | Finance |
| drpl | Drupal |
| wprs | WordPress |

### Common Action Codes

| Code | Meaning |
|------|---------|
| migr | Migration |
| setup | Setup/installation |
| audt | Audit |
| docs | Documentation |
| fix | Fix/repair |

---

*Back to [Templates Index](index.md)*
