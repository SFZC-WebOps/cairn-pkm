# Home Docs

The source of truth for each track.

---

## Overview

Every track (project or area) has a **home doc** — the single file that contains everything about that track.

**Naming:** `_{track}-home.md`
- `_p042-blog-migr-home.md`
- `_area-finance-home.md`

**Location:** Root of the track folder.

The underscore prefix sorts it to the top of the folder.

---

## Structure

### Project Home Doc

```markdown
---
project_id: p###-xxxx-yyyy
title: "Full Project Name"
type: project
status: active
progress: 0
created: YYYY-MM-DD
modified: YYYY-MM-DD
tags:
  - project
---

## Overview

What this project accomplishes and why.

## Success Criteria

- [ ] Measurable outcome 1
- [ ] Measurable outcome 2
- [ ] Measurable outcome 3

## Current Focus

What's active right now.

## Notes

Working scratchpad — open questions, pending decisions.

---

## Log

---

YYYY-MM-DD HH:MM - Type - Summary
Details if needed.

---

YYYY-MM-DD HH:MM - Type - Summary
Details if needed.
```

### Area Home Doc

```markdown
---
title: "Area Name"
type: area
status: active
created: YYYY-MM-DD
modified: YYYY-MM-DD
tags:
  - area
---

## Overview

What this area covers and why it matters.

## Current Focus

What's active right now in this area.

## Standards

- Standard to maintain
- Standard to maintain

## Notes

Working scratchpad — open questions, things to remember.

---

## Log

---

YYYY-MM-DD HH:MM - Type - Summary
Details if needed.
```

---

## Frontmatter Fields

### Required

| Field | Project | Area | Description |
|-------|---------|------|-------------|
| title | ✓ | ✓ | Full name |
| type | ✓ | ✓ | `project` or `area` |
| status | ✓ | ✓ | Current state |
| created | ✓ | ✓ | Creation date |
| modified | ✓ | ✓ | Last update |

### Project-Specific

| Field | Description |
|-------|-------------|
| project_id | `p###-xxxx-yyyy` identifier |
| progress | 0-100 percentage |

### Status Values

| Status | Meaning |
|--------|---------|
| active | Currently in progress |
| planning | Not yet started |
| blocked | Waiting on dependency |
| review | Under review |
| complete | Finished |
| archived | No longer active |

---

## Sections

### Overview

2-3 sentences explaining:
- What this is
- Why it matters
- Key context

### Success Criteria (Projects)

Measurable outcomes that define "done":
- Specific deliverables
- Checkboxes for tracking
- Clear completion criteria

### Standards (Areas)

Ongoing standards to maintain:
- Quality thresholds
- Regular responsibilities
- Key metrics

### Current Focus

What's active **right now**:
- Today's priority
- This week's goal
- Active blockers

Update frequently to stay useful.

### Notes

Working scratchpad:
- Open questions
- Pending decisions
- Ideas to explore
- Things to remember

### Log

Chronological record of activity:
- Newest entries at bottom (append-only)
- Entries separated by horizontal rules
- Timestamped and typed

---

## Log Entries

### Format

```
YYYY-MM-DD HH:MM - Type - Summary
Optional details, context, or notes.
- Files: action filenames
- AI Contribution: what assistant did
```

### Entry Types

| Type | Use for |
|------|---------|
| Development | Code, scripts, technical work |
| Configuration | Settings, deployment, setup |
| Documentation | Docs, guides, notes |
| Meeting | Discussions, calls |
| Decision | Choices made |
| BLOCKED | Obstacles, dependencies |
| Testing | Verification, QA |
| Deployment | Releases, launches |
| Review | Assessments, audits |

### Good Log Entries

```
2025-01-28 14:30 - Configuration - Updated SSL certificate chain
Resolved validation errors by adding intermediate certificate.
- Files: Modified nginx.conf
- AI Contribution: Assistant diagnosed certificate chain issue

2025-01-27 10:00 - Meeting - Kickoff with stakeholders
Agreed on timeline and success criteria.
Next: Draft project plan by Friday.

2025-01-26 16:45 - BLOCKED - Waiting on API access
Requested credentials from IT. Expected by Tuesday.
```

### Bad Log Entries

```
Did stuff today.          # Too vague
Working on things.        # No specifics
Updated file.             # Which file? What change?
```

---

## Using Home Docs

### Daily

1. Check **Current Focus** for today's priority
2. Update as work progresses
3. Add log entries for significant activity

### Weekly

1. Review **Overview** — still accurate?
2. Update **Progress** (projects)
3. Clean up **Notes**
4. Ensure log is current

### With LLM Commands

- `!hi-[track]` reads home doc for overview
- `!bye` generates log entries for pasting
- Context persists between commands

---

## Templates

- [track-project](templates/track-project.md) — Creates project with home doc
- [track-area](templates/track-area.md) — Creates area with home doc

---

*Back to [Folders](folders.md) | [README](README.md)*
