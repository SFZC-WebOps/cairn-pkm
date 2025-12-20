# !hi - Work Session
*Type: Interactive | Version: 1.0.0 | Updated: 2025-12-19*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!hi` | Open main menu |
| `!hi-[target]` | Open focused session on track |

**Session model:** `!hi` opens → work via menus or freeform → `!bye` closes with logging

---

## Initialization

Per `cmd-shared-patterns.md`

---

## !hi — Main Menu

```
🧭 What would you like to do?

1. Open a project or area
2. Create a task
3. Edit a task
4. Create a project
5. Create an area
6. Create/edit an object
7. Quick note
8. Record skill evidence
9. Close session

Enter number or describe what you need:
```

### Input Handling

| Input | Action |
|-------|--------|
| Number (1-9) | Route to selection |
| Direct command (`!task-c`) | Execute command |
| Natural language | Interpret and route |
| Question | Answer using context |

### Routing

| # | Action |
|---|--------|
| 1 | "Which track?" → `!hi-[target]` |
| 2 | `!task-c` flow |
| 3 | `!task-e` flow |
| 4 | Create project flow |
| 5 | Create area flow |
| 6 | `!object` flow |
| 7 | `!quicknote` flow |
| 8 | `!skills` flow |
| 9 | `!bye` flow |

---

## !hi-[target] — Focused Session

### Summary Display

```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
EXTRACT: status, progress, last 3 log entries
SCAN: tasks/ for active count, overdue count
```

```
🧭 [target]
Status: [status] | Progress: [progress]%

Recent:
- [date]: [log entry]
- [date]: [log entry]
- [date]: [log entry]

[N] active tasks, [N] overdue

1. View full status + tasks
2. Create a task
3. Edit a task
4. Add log entry
5. Update status/progress
6. Switch track
7. Close session
```

### Routing

| # | Action |
|---|--------|
| 1 | Display full status + all tasks |
| 2 | `!task-c` with track pre-filled |
| 3 | "Which task?" → `!task-e` |
| 4 | Add log entry flow |
| 5 | Update status/progress flow |
| 6 | "Which track?" → new `!hi-[target]` |
| 7 | `!bye` flow |

---

## Create Area Flow

*Main menu → 5*

### Prompts

```
Creating new area...

1. Domain name? (becomes area-{domain})
   Examples: finance, health, webops, personal
   
2. Brief description? (1-2 sentences)
```

### Generation

```
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/area-{domain}/
CONSTRUCT: home_doc = {folder_path}_area-{domain}-home.md
CREATE: subfolders resources/, tasks/, zzz/
```

### Home Doc Template

```yaml
---
title: {user input}
type: area
status: active
created: {YYYY-MM-DD}
modified: {YYYY-MM-DD}
---

## Overview
{user description}

## Current Focus

## Notes

---

## Log

---

{YYYY-MM-DD HH:MM} - Setup - Area created

---
```

Output per file_operations setting. See `cmd-output-behavior.md`.

---

## Create Project Flow

*Main menu → 4*

### Prompts

```
Creating new project...

1. System code? (4 letters) - blog, home, work, sfzc
2. Action code? (4 letters) - migr, setup, audt, docs
3. Project title?
4. Brief description? (1-2 sentences)
```

### Generation

```
SCAN: {VAULT_PATH}/Tracks/ for existing p###-* folders
CALCULATE: next_number = highest + 1, zero-padded to 3 digits
CONSTRUCT: project_id = p{next_number}-{system}-{action}
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/{project_id}/
CREATE: subfolders resources/, tasks/, zzz/
```

### Home Doc Template

```yaml
---
project_id: {project_id}
title: {user input}
type: project
status: active
progress: 0
created: {YYYY-MM-DD}
modified: {YYYY-MM-DD}
---

## Overview
{user description}

## Current Focus

## Notes

---

## Log

---

{YYYY-MM-DD HH:MM} - Setup - Project created

---
```

Output per file_operations setting. See `cmd-output-behavior.md`.

---

## Add Log Entry Flow

*Focused session → 4*

### Prompts

```
Adding log entry to [target]...

Entry type? Development | Configuration | Documentation | Meeting | Decision | Testing | Deployment | Review | Other

What happened? (1-2 lines):
```

### Action

```
READ: home doc
APPEND to Log section: "{YYYY-MM-DD HH:MM} - {type} - {user input}"
UPDATE: modified date in frontmatter
```

Output per file_operations setting. Returns to focused session menu.

---

## Update Status/Progress Flow

*Focused session → 5*

### Prompts

```
Updating [target]...

What to update? Status | Progress | Both

Status options: active | on-hold | blocked | complete | archived
Progress: 0-100
```

### Action

```
READ: home doc
UPDATE: frontmatter fields
APPEND to Log: "{YYYY-MM-DD HH:MM} - Update - {changes}"
UPDATE: modified date
```

Output per file_operations setting. Returns to focused session menu.

---

## View Full Status + Tasks

*Focused session → 1*

```
READ: home doc + all tasks in tasks/
FILTER: status != complete
SORT: priority (critical → low), then created_date

OUTPUT:
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Type:** [area|project]

### Summary
[Overview section]

### Current Focus
[Current Focus section]

### Recent Activity
[Last 10 log entries]

### Active Tasks ([N] tasks, [X] overdue, [Y] due this week)

┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 [title]
Status: [status] | Priority: [priority] | Effort: [effort]
Created: [date] | Due: [date or "(none)"] [⚠️ OVERDUE if applicable]

Subtasks:
  └─ [ ] or [x] [subtask text]
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Repeat for each task]
```

Returns to focused session menu.

---

## Context Persistence

```
SET on !hi: session_active = true
TRACK: tracks_viewed[], files_created[], files_edited[], decisions[], log_entries_added[]
USE on !bye: Full context for log generation
CLEAR on !bye: Session state reset
```

---

## Freeform Input Examples

**Main menu:**
- "Create a task for the storage migration" → Task creation, infers track
- "What projects are active?" → Lists active projects
- "Show me p14" → `!hi-p14`

**Focused session:**
- "Mark the SSL task complete" → Finds task, updates status
- "Add a note that we're waiting on vendor" → Log entry flow
- "Create a task to follow up next week" → Task creation with context

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Ambiguous input | Ask clarifying question |
| Track not found | "Track not found. Available: [list]" |
| Can't interpret request | "I didn't understand. Try a number or rephrase?" |
| Folder already exists | "Already exists. Open it instead?" |
| Invalid progress | "Progress must be 0-100" |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.9.1 | 2025-12-19 | Updated command references |
| 0.9.0 | 2025-12-19 | Reset to pre-release versioning |
