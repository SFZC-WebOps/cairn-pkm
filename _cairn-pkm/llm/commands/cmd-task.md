/mnt/project/# !task - Task Management
*Type: Read/Write | Version: 0.8.0 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | Purpose | Output Mode |
|---------|---------|-------------|
| `!task-c` | Generate new task file from conversation | Per cairn-pkm-user-prefs.yaml |
| `!task-e` | Generate edited task from uploaded file | Per cairn-pkm-user-prefs.yaml |

**Location:** `{VAULT_PATH}/Tracks/[track]/tasks/`

**Workflow:**
- **Create:** Discuss work Ã¢â€ â€™ `!task-c` Ã¢â€ â€™ Review draft Ã¢â€ â€™ Provide required fields Ã¢â€ â€™ `done` Ã¢â€ â€™ Output per prefs
- **Edit:** Upload task file Ã¢â€ â€™ Discuss updates Ã¢â€ â€™ `!task-e` Ã¢â€ â€™ Review changes Ã¢â€ â€™ `done` Ã¢â€ â€™ Output per prefs

---

## Requirements

**Assistant Capabilities:**
- Conversation context analysis
- YAML frontmatter generation
- Timestamp generation
- Interactive editing loop
- File system access (if file_operations = write or confirm)

**User Configuration:**
- `/mnt/project/cairn-pkm-user-prefs.yaml` Ã¢â‚¬â€ file_operations setting, defaults

**Vault Structure:**
- `Tracks/[track]/tasks/` Ã¢â‚¬â€ Task files per track

---

## Initialization

```
READ: /mnt/project/cairn-pkm-user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - default_assignee (default: "")
  - timezone (default: "America/Los_Angeles")

HARDCODED DEFAULTS:
  - default_priority = "medium"
  - default_status = "active"
  - default_effort = "moderate"
  - default_phase = "executing"
```

---

## !task-c Ã¢â‚¬â€ Create Task

### Execution

1. Display current date/time
2. Read cairn-pkm-user-prefs.yaml for defaults and output mode
3. Infer track context (from !hi, conversation, or ask user)
4. Extract from conversation: title, types, and any mentioned fields
5. Generate filename: `{YYYYMMDD}-{slug}.md`
6. Show draft, prompt for required fields (priority, status)
7. Interactive edit loop until user types `done`
8. Output per file_operations setting

### Required Fields

| Field | Description |
|-------|-------------|
| title | Task name (extracted or provided) |
| project | Track identifier |
| created_date | Auto-set to today |
| priority | `low | medium | high | critical` (default from prefs) |
| status | `active | blocked | complete | deferred | onhold | scheduled | waiting` (default from prefs) |

### Optional Fields

| Field | Description |
|-------|-------------|
| due_date | YYYY-MM-DD |
| assignee | From default_assignee in prefs |
| phase | `planning | executing | testing | closing` (default from prefs) |
| effort | `simple | moderate | complex` (default from prefs) |
| type | Array of category/subcategory |
| parent_task | Filename of parent task |

### Subtask Handling

Subtasks are **body content only** Ã¢â‚¬â€ not stored in frontmatter.

Assistant scans conversation for task breakdowns:
- "Steps are: X, Y, Z"
- "Need to: first X, then Y, then Z"
- Numbered or bulleted lists describing work items

Each extracted subtask becomes a checkbox in the body's Subtasks section.

### Interactive Commands

```
done                 - Finalize and output per prefs
edit [field]         - Modify any field
status [value]       - active, blocked, complete, deferred, onhold, scheduled, waiting
priority [value]     - low, medium, high, critical
phase [value]        - planning, executing, testing, closing
effort [value]       - simple, moderate, complex
type add [type]      - Add type tag
type remove [type]   - Remove type tag
```

### File Template

```yaml
---
title: "{title}"
project: "{track}"
created_date: YYYY-MM-DD
due_date: 
assignee: {default_assignee from prefs}
parent_task: 
priority: {from prefs or user input}
status: {from prefs or user input}
phase: {from prefs}
effort: {from prefs}
type:
  - {suggested types}
last_update:
  - "YYYY-MM-DD: Created task - {brief summary}"
---

### Task History

- YYYY-MM-DD: Created task - {brief summary}

---

### Subtasks

- [ ] {subtask 1 text}
- [ ] {subtask 2 text}

---

### Notes


---
```

### Slug Generation

From title, extract 3-4 key words, lowercase, hyphenated, max 40 chars.

| Title | Slug |
|-------|------|
| "Fix SSL certificate chain validation" | `ssl-cert-chain-validation` |
| "Set up new environment" | `setup-new-environment` |

### Output Phase

```
CONSTRUCT: filepath = {VAULT_PATH}/Tracks/{track}/tasks/{filename}
CONSTRUCT: content = {complete file with frontmatter and body}

CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

---

## !task-e Ã¢â‚¬â€ Edit Task

### Execution

1. Display current date/time
2. Read cairn-pkm-user-prefs.yaml for output mode
3. Read uploaded task file
4. Generate history entry from conversation context
5. Interactive edit loop until user types `done`
6. Output per file_operations setting

### Input Required

User must upload the task file before running `!task-e`

### History Entry Format

One line, under 200 characters, past tense, outcome-focused.

**Good:**
- `2025-11-08: Approved configuration change - implementation scheduled`
- `2025-11-04: Conducted test; identified tracking failure`

**Bad:**
- `Working on stuff` (vague)
- `I updated the configuration and then tested it` (verbose, present tense)

### Subtask Updates

When editing:
- New subtasks mentioned in conversation are added to the body's Subtasks section
- Existing subtask status changes are reflected in checkboxes
- Assistant does NOT modify frontmatter for subtask changes

### Interactive Commands

```
done                 - Finalize and output per prefs
edit                 - Modify history entry text
status [value]       - active, blocked, complete, deferred, onhold, scheduled, waiting
priority [value]     - low, medium, high, critical
phase [value]        - planning, executing, testing, closing
effort [value]       - simple, moderate, complex
due [YYYY-MM-DD]     - Set due date
type add [type]      - Add type tag
field [name] [value] - Update any field
```

### Output Phase

```
CONSTRUCT: filepath = {original file path}
CONSTRUCT: content = {complete edited file}

CALL: OUTPUT_FILE(filepath, content)
```

---

## Field Inference Guide

| Field | Look For |
|-------|----------|
| priority | "urgent/critical/emergency" Ã¢â€ â€™ critical; "important/ASAP" Ã¢â€ â€™ high; "nice to have/backlog" Ã¢â€ â€™ low |
| status | "working on" Ã¢â€ â€™ active; "blocked by" Ã¢â€ â€™ blocked; "waiting for" Ã¢â€ â€™ waiting; "planned for" Ã¢â€ â€™ scheduled |
| phase | "scoping/planning" Ã¢â€ â€™ planning; "building/implementing" Ã¢â€ â€™ executing; "testing/verifying" Ã¢â€ â€™ testing; "wrapping up" Ã¢â€ â€™ closing |
| effort | "quick fix" Ã¢â€ â€™ simple; "complex/involved" Ã¢â€ â€™ complex; default Ã¢â€ â€™ moderate |
| type | Match technical domains mentioned in conversation |

---

## Output Examples

### Draft Phase

```
Current Date/Time: December 18, 2025 at 14:30 PST
Creating new task...

TASK DRAFT
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Filename: 20251218-wp-environment-setup.md

title: Set up new WordPress environment
project: [need track - which project/area?]
type:
  - webops/wordpress
  - webops/hosting

Subtasks detected (3):
  - Create WP Engine account
  - Provision staging
  - Install theme
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â

Give me the priority and status values for this new task:
- priority: low, medium, high, critical
- status: active, blocked, complete, deferred, onhold, scheduled, waiting

Also, which track does this belong to?
```

### Completion (display mode)

```
Ã°Å¸â€œâ€ž FILE CONTENT
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Filename: 20251218-wp-environment-setup.md
Path: {VAULT_PATH}/Tracks/area-webops/tasks/

{complete file content with frontmatter and body}

Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Copy this content and save to the path above.

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

### Completion (write mode)

```
Ã¢Å“â€œ Created {VAULT_PATH}/Tracks/area-webops/tasks/20251218-wp-environment-setup.md

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

### Completion (confirm mode)

```
Ã°Å¸â€œâ€ž PROPOSED FILE
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Filename: 20251218-wp-environment-setup.md
Path: {VAULT_PATH}/Tracks/area-webops/tasks/

{complete file content}

Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Write this file? (yes/no)
```

Then on confirmation:

```
Ã¢Å“â€œ Created {VAULT_PATH}/Tracks/area-webops/tasks/20251218-wp-environment-setup.md

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No task file uploaded (!task-e) | Ask user to upload the task file |
| Can't infer title (!task-c) | Ask user to describe the task |
| Invalid priority/status | Show valid options, stay in edit loop |
| Missing required field on done | List missing fields, stay in edit loop |
| cairn-pkm-user-prefs.yaml missing | Use defaults: display mode, local target, empty assignee |
| Write fails (write/confirm mode) | Report error, fall back to display mode |

---

## File Paths

| Purpose | Path |
|---------|------|
| User prefs | `/mnt/project/cairn-pkm-user-prefs.yaml` |
| Task location | `{VAULT_PATH}/Tracks/[track]/tasks/` |
| Filename pattern | `{YYYYMMDD}-{slug}.md` |

---

## Integration

| Works With | Notes |
|------------|-------|
| !hi-[target] | Uses stored context for track inference |
| Obsidian Tasks | Tasks visible via plugin; checkboxes in body are native |
| Dataview | Query tasks by frontmatter fields across all tracks |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.8.0 | 2025-12-19 | Reset to pre-release versioning (was 5.0) |
| 5.0 | 2025-12-18 | Added user-prefs support, configurable output mode |
| 4.2 | 2025-12-16 | Previous version (display only) |
