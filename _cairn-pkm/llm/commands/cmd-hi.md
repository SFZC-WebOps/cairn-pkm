# !hi - Work Session
*Type: Interactive | Version: 0.9.1 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | Purpose | Output |
|---------|---------|--------|
| `!hi` | Open work session | Main menu |
| `!hi-[target]` | Open focused session | Brief summary + action menu |

**Session model:** `!hi` opens, work happens via menus or freeform, `!bye` closes with logging.

---

## Requirements

**Assistant Capabilities:**
- File system read access (always)
- File system write access (if file_operations = write or confirm)
- Directory creation (if file_operations = write or confirm)
- YAML frontmatter parsing
- Session context tracking
- Natural language interpretation

**User Configuration:**
- `_local/user-prefs.yaml` Ã¢â‚¬â€ file_operations setting, timezone, defaults

**Vault Structure:**
- `Tracks/` Ã¢â‚¬â€ Projects and areas
- `Tracks/*/tasks/` Ã¢â‚¬â€ Task files per track
- `Tracks/*/_*-home.md` Ã¢â‚¬â€ Home docs

---

## Initialization

```
READ: {VAULT_PATH}/_local/user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - default_assignee (default: "")
  - timezone (default: "America/Los_Angeles")
```

---

## !hi Ã¢â‚¬â€ Main Menu

### Output
```
Ã°Å¸Â§Â­ What would you like to do?

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

| Input Type | Action |
|------------|--------|
| Number (1-9) | Route to selection |
| Direct command (`!task-c`) | Execute command |
| Natural language | Interpret and route or respond |
| Question | Answer using available context |

### Menu Routing

| Selection | Action |
|-----------|--------|
| 1 | "Which track?" Ã¢â€ â€™ `!hi-[target]` flow |
| 2 | `!task-c` flow |
| 3 | `!task-e` flow |
| 4 | Create project flow |
| 5 | Create area flow |
| 6 | `!object` flow Ã¢â€ â€™ "Create or edit?" |
| 7 | `!quicknote` flow |
| 8 | `!skills` flow |
| 9 | `!bye` flow |

---

## !hi-[target] Ã¢â‚¬â€ Focused Session

### Phase 1: Brief Summary
```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
EXTRACT: status, progress
SCAN: tasks/ folder for active count, overdue count
EXTRACT: Last 3 log entries
```

### Output
```
Ã°Å¸Â§Â­ [target]
Status: [status] | Progress: [progress]%

Recent:
- [date]: [log entry]
- [date]: [log entry]
- [date]: [log entry]

[N] active tasks, [N] overdue

What would you like to do?

1. View full status + tasks
2. Create a task
3. Edit a task
4. Add log entry
5. Update status/progress
6. Switch track
7. Close session

Enter number or describe what you need:
```

### Input Handling

| Input Type | Action |
|------------|--------|
| Number (1-7) | Route to selection |
| Direct command | Execute with track context |
| Task reference ("the SSL task") | Resolve and act |
| Natural language | Interpret and route or respond |
| Question | Answer using track context |

### Menu Routing

| Selection | Action |
|-----------|--------|
| 1 | Display full status + all tasks |
| 2 | `!task-c` flow with track pre-filled |
| 3 | "Which task?" Ã¢â€ â€™ `!task-e` flow |
| 4 | Add log entry flow |
| 5 | Update status/progress flow |
| 6 | "Which track?" Ã¢â€ â€™ new `!hi-[target]` |
| 7 | `!bye` flow |

---

## Create Area Flow

*Accessed via: Main menu Ã¢â€ â€™ 5*

### Prompts
```
Creating new area...

1. Domain name? (becomes area-{domain})
   Examples: finance, health, webops, personal
   
2. Brief description? (1-2 sentences)
```

### Auto-generated
```
CONSTRUCT: domain_slug = lowercase, hyphenated input
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/area-{domain}/
CONSTRUCT: home_doc_path = {folder_path}_area-{domain}-home.md
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

### Output Phase
```
SWITCH file_operations:

  CASE "display":
    OUTPUT: "Ã°Å¸â€œÂ CREATE THESE FOLDERS:"
    OUTPUT: "  {VAULT_PATH}/Tracks/area-{domain}/"
    OUTPUT: "  {VAULT_PATH}/Tracks/area-{domain}/resources/"
    OUTPUT: "  {VAULT_PATH}/Tracks/area-{domain}/tasks/"
    OUTPUT: "  {VAULT_PATH}/Tracks/area-{domain}/zzz/"
    OUTPUT: ""
    CALL: OUTPUT_FILE(home_doc_path, content)

  CASE "confirm":
    OUTPUT: "Will create:"
    OUTPUT: "  - Folder: {folder_path}"
    OUTPUT: "  - Subfolders: resources/, tasks/, zzz/"
    OUTPUT: "  - Home doc: _area-{domain}-home.md"
    OUTPUT: ""
    OUTPUT: "Proceed? (yes/no)"
    WAIT FOR: confirmation
    IF confirmed: EXECUTE writes
    ELSE: OUTPUT "Area creation cancelled"

  CASE "write":
    CREATE: {VAULT_PATH}/Tracks/area-{domain}/
    CREATE: {VAULT_PATH}/Tracks/area-{domain}/resources/
    CREATE: {VAULT_PATH}/Tracks/area-{domain}/tasks/
    CREATE: {VAULT_PATH}/Tracks/area-{domain}/zzz/
    CREATE: home doc file
    OUTPUT: "Ã¢Å“â€œ Area created: area-{domain}"
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

### Completion
```
Ã¢Å“â€œ Area created: area-{domain}
  Path: {VAULT_PATH}/Tracks/area-{domain}/
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Ã°Å¸Â§Â­ What would you like to do?

1. Open area-{domain}
2. Create a task for this area
3. Return to main menu

Enter number or describe what you need:
```

---

## Create Project Flow

*Accessed via: Main menu Ã¢â€ â€™ 4*

### Prompts
```
Creating new project...

1. System code? (4 letters)
   Examples: blog, home, work, sfzc, finc
   
2. Action code? (4 letters)
   Examples: migr, setup, audt, docs, fixs
   
3. Project title?

4. Brief description? (1-2 sentences)
```

### Auto-generated
```
SCAN: {VAULT_PATH}/Tracks/ for existing p###-* folders
CALCULATE: next_number = highest existing + 1, zero-padded to 3 digits
CONSTRUCT: project_id = p{next_number}-{system}-{action}
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/{project_id}/
CONSTRUCT: home_doc_path = {folder_path}_{project_id}-home.md
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

### Output Phase
```
SWITCH file_operations:

  CASE "display":
    OUTPUT: "Ã°Å¸â€œÂ CREATE THESE FOLDERS:"
    OUTPUT: "  {VAULT_PATH}/Tracks/{project_id}/"
    OUTPUT: "  {VAULT_PATH}/Tracks/{project_id}/resources/"
    OUTPUT: "  {VAULT_PATH}/Tracks/{project_id}/tasks/"
    OUTPUT: "  {VAULT_PATH}/Tracks/{project_id}/zzz/"
    OUTPUT: ""
    CALL: OUTPUT_FILE(home_doc_path, content)

  CASE "confirm":
    OUTPUT: "Will create:"
    OUTPUT: "  - Folder: {folder_path}"
    OUTPUT: "  - Subfolders: resources/, tasks/, zzz/"
    OUTPUT: "  - Home doc: _{project_id}-home.md"
    OUTPUT: ""
    OUTPUT: "Proceed? (yes/no)"
    WAIT FOR: confirmation
    IF confirmed: EXECUTE writes
    ELSE: OUTPUT "Project creation cancelled"

  CASE "write":
    CREATE: {VAULT_PATH}/Tracks/{project_id}/
    CREATE: {VAULT_PATH}/Tracks/{project_id}/resources/
    CREATE: {VAULT_PATH}/Tracks/{project_id}/tasks/
    CREATE: {VAULT_PATH}/Tracks/{project_id}/zzz/
    CREATE: home doc file
    OUTPUT: "Ã¢Å“â€œ Project created: {project_id}"
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

### Completion
```
Ã¢Å“â€œ Project created: {project_id}
  Path: {VAULT_PATH}/Tracks/{project_id}/
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Ã°Å¸Â§Â­ What would you like to do?

1. Open {project_id}
2. Create a task for this project
3. Return to main menu

Enter number or describe what you need:
```

---

## Add Log Entry Flow

*Accessed via: Focused session Ã¢â€ â€™ 4*

### Prompts
```
Adding log entry to [target]...

Entry type?
1. Development
2. Configuration
3. Documentation
4. Meeting
5. Decision
6. Testing
7. Deployment
8. Review
9. Other

Enter number or type directly:

What happened? (1-2 lines):
```

### Action
```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md

CONSTRUCT: log_entry = "
---

{YYYY-MM-DD HH:MM} - {type} - {user input}

---
"

CONSTRUCT: updated_content = original with log_entry appended to Log section
UPDATE: modified date in frontmatter
```

### Output Phase
```
SWITCH file_operations:

  CASE "display":
    OUTPUT: "Ã°Å¸â€œâ€¹ ADD THIS LOG ENTRY TO:"
    OUTPUT: "{VAULT_PATH}/Tracks/[target]/_*-home.md"
    OUTPUT: ""
    OUTPUT: "In the Log section, add:"
    OUTPUT: "---"
    OUTPUT: "{YYYY-MM-DD HH:MM} - {type} - {user input}"
    OUTPUT: "---"

  CASE "confirm":
    OUTPUT: "Will append to [target] log:"
    OUTPUT: "  {YYYY-MM-DD HH:MM} - {type} - {user input}"
    OUTPUT: ""
    OUTPUT: "Proceed? (yes/no)"
    WAIT FOR: confirmation
    IF confirmed: WRITE file
    ELSE: OUTPUT "Log entry cancelled"

  CASE "write":
    WRITE: updated file
    OUTPUT: "Ã¢Å“â€œ Log entry added to [target]"
```

### Completion
```
Ã¢Å“â€œ Log entry added to [target]
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Ã°Å¸Â§Â­ [target]
[return to focused session menu]
```

---

## Update Status/Progress Flow

*Accessed via: Focused session Ã¢â€ â€™ 5*

### Prompts
```
Updating [target]...

What do you want to update?
1. Status
2. Progress
3. Both

Enter number:
```

**If Status:**
```
Current status: [current]

New status?
1. active
2. on-hold
3. blocked
4. complete
5. archived

Enter number or type directly:
```

**If Progress:**
```
Current progress: [current]%

New progress (0-100):
```

### Action
```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
UPDATE: frontmatter fields
UPDATE: modified date

CONSTRUCT: log_entry = "{YYYY-MM-DD HH:MM} - Update - Status changed to {status} / Progress updated to {progress}%"
APPEND: log_entry to Log section
```

### Output Phase
```
SWITCH file_operations:

  CASE "display":
    OUTPUT: "Ã°Å¸â€œâ€¹ UPDATE THIS FILE:"
    OUTPUT: "{VAULT_PATH}/Tracks/[target]/_*-home.md"
    OUTPUT: ""
    OUTPUT: "In frontmatter, set:"
    IF status changed: OUTPUT: "  status: {new_status}"
    IF progress changed: OUTPUT: "  progress: {new_progress}"
    OUTPUT: "  modified: {YYYY-MM-DD}"
    OUTPUT: ""
    OUTPUT: "In Log section, add:"
    OUTPUT: "---"
    OUTPUT: "{log_entry}"
    OUTPUT: "---"

  CASE "confirm":
    OUTPUT: "Will update [target]:"
    IF status changed: OUTPUT: "  - Status: {old} Ã¢â€ â€™ {new}"
    IF progress changed: OUTPUT: "  - Progress: {old}% Ã¢â€ â€™ {new}%"
    OUTPUT: ""
    OUTPUT: "Proceed? (yes/no)"
    WAIT FOR: confirmation
    IF confirmed: WRITE file
    ELSE: OUTPUT "Update cancelled"

  CASE "write":
    WRITE: updated file
    OUTPUT: "Ã¢Å“â€œ [target] updated"
```

### Completion
```
Ã¢Å“â€œ [target] updated
  Status: {status} | Progress: {progress}%
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Ã°Å¸Â§Â­ [target]
[return to focused session menu]
```

---

## View Full Status + Tasks

*Accessed via: Focused session Ã¢â€ â€™ 1*

### Output
```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
READ: All files in {VAULT_PATH}/Tracks/[target]/tasks/
PARSE: Frontmatter for each task
FILTER: status != complete
SORT: By priority (critical Ã¢â€ â€™ high Ã¢â€ â€™ medium Ã¢â€ â€™ low), then created_date

OUTPUT:
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Type:** [area|project]

### Summary
[Overview section from home doc]

### Current Focus
[Current Focus section from home doc]

### Recent Activity
[Last 10 log entries]

### Active Tasks ([N] tasks, [X] overdue, [Y] due this week)

Ã¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€Â
Ã°Å¸â€œâ€¹ [title]
Status: [status] | Priority: [priority] | Effort: [effort]
Created: [created_date] | Due: [due_date OR "(none)"] [Ã¢Å¡Â Ã¯Â¸Â OVERDUE if applicable]

[IF subtasks exist in body:]
Subtasks:
  Ã¢â€â€Ã¢â€â‚¬ [ ] or [x] [subtask text]
Ã¢â€â€”Ã¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€ÂÃ¢â€Â

[Repeat for each task]

Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Ã°Å¸Â§Â­ [target]
[return to focused session menu]
```

---

## Freeform Input Examples

**From main menu:**
- `"I need to create a task for the storage migration"` Ã¢â€ â€™ Task creation, infers track
- `"What projects are active?"` Ã¢â€ â€™ Lists active projects
- `"Show me p14"` Ã¢â€ â€™ `!hi-p14` flow
- `"!skills"` Ã¢â€ â€™ Direct to skill evidence flow

**From focused session:**
- `"Mark the SSL task complete"` Ã¢â€ â€™ Finds task, updates status
- `"What's blocking progress?"` Ã¢â€ â€™ Reviews tasks, identifies blockers
- `"Add a note that we're waiting on vendor"` Ã¢â€ â€™ Log entry flow
- `"Create a task to follow up next week"` Ã¢â€ â€™ Task creation with context

---

## Context Persistence

```
SET on !hi or !hi-[target]: session_active = true
TRACK: 
  - tracks_viewed[]
  - files_created[]
  - files_edited[]
  - decisions[]
  - log_entries_added[]
USE on !bye: Full session context for log generation
CLEAR on !bye: Session state reset
```

---

## Completion Pattern

After each action completes:
```
Ã¢Å“â€œ [Action completed message]
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

[Return to menu for current context]
```

Only `!bye` ends the session.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Ambiguous input | Ask clarifying question |
| Track not found | "Track not found. Available: [list recently used or suggest search]" |
| Can't interpret request | "I didn't understand. Try a number or rephrase?" |
| Folder already exists (create) | "Area/project already exists. Open it instead?" |
| Invalid progress value | "Progress must be 0-100. Try again:" |

---

## File Paths

| Purpose | Path |
|---------|------|
| Tracks | `{VAULT_PATH}/Tracks/` |
| Home docs | `{VAULT_PATH}/Tracks/[target]/_*-home.md` |
| Tasks | `{VAULT_PATH}/Tracks/[target]/tasks/` |
| Resources | `{VAULT_PATH}/Tracks/[target]/resources/` |
| Archive | `{VAULT_PATH}/Tracks/[target]/zzz/` |

---

## Version History

| Version | Date | Changes |
| 0.9.1 | 2025-12-19 | Updated command references: !obj→!object, !qn→!quicknote, !sk→!skills |
|---------|------|---------|
| 0.9.0 | 2025-12-19 | Reset to pre-release versioning (was 12.0) |
| 12.0 | 2025-12-19 | Refactored as interactive session with menus + freeform input; added create area, create project, add log entry, update status flows |
| 11.1 | 2025-12-16 | Deep-dive view with task display |
