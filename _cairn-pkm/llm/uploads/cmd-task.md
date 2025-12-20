# !task - Task Management
*Type: Read/Write | Version: 0.9.0 | Updated: 2025-12-19*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!task-c` | Create task from conversation |
| `!task-e` | Edit uploaded task file |

**Location:** `{VAULT_PATH}/Tracks/{track}/tasks/`

**Workflow:**
- **Create:** Discuss work → `!task-c` → Review draft → Provide required fields → `done`
- **Edit:** Upload task → Discuss updates → `!task-e` → Review → `done`

---

## Initialization

**Common:** Per `cmd-shared-patterns.md`

**Task-specific defaults:**
```
default_priority = "medium"
default_status = "active"
default_effort = "moderate"
default_phase = "executing"
```

---

## !task-c — Create Task

### Execution

1. Display current date/time
2. Infer track context (from !hi, conversation, or ask user)
3. Extract from conversation: title, types, mentioned fields
4. Generate filename: `{YYYYMMDD}-{slug}.md`
5. Show draft, prompt for required fields
6. Interactive edit loop until `done`
7. Output per file_operations setting

### Fields

**Required:**

| Field | Description |
|-------|-------------|
| title | Task name (extracted or provided) |
| project | Track identifier |
| priority | `low | medium | high | critical` |
| status | `active | blocked | complete | deferred | onhold | scheduled | waiting` |

**Optional:** due_date, assignee, phase, effort, type[], parent_task

### Subtask Handling

Subtasks are **body content only** (not frontmatter). Assistant scans for task breakdowns and creates checkboxes in Subtasks section.

### Interactive Commands

```
done                 - Finalize and output
edit [field]         - Modify any field
status [value]       - Change status
priority [value]     - Change priority
phase [value]        - planning | executing | testing | closing
effort [value]       - simple | moderate | complex
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
assignee: {default_assignee}
parent_task: 
priority: {priority}
status: {status}
phase: {phase}
effort: {effort}
type:
  - {types}
last_update:
  - "YYYY-MM-DD: Created task - {summary}"
---

### Task History
- YYYY-MM-DD: Created task - {summary}

---

### Subtasks
- [ ] {subtask}

---

### Notes

---
```

### Slug Generation

From title: 3-4 key words, lowercase, hyphenated, max 40 chars.

| Title | Slug |
|-------|------|
| "Fix SSL certificate chain validation" | `ssl-cert-chain-validation` |
| "Set up new environment" | `setup-new-environment` |

---

## !task-e — Edit Task

### Execution

1. Display current date/time
2. Read uploaded task file
3. Generate history entry from conversation
4. Interactive edit loop until `done`
5. Output per file_operations setting

**Requires:** User uploads task file first

### History Entry Format

One line, under 200 characters, past tense, outcome-focused.

**Good:** `2025-11-08: Approved configuration change - implementation scheduled`
**Bad:** `Working on stuff` (vague), `I updated the configuration...` (verbose)

### Interactive Commands

```
done                 - Finalize and output
edit                 - Modify history entry
status [value]       - Change status
priority [value]     - Change priority
phase [value]        - Change phase
effort [value]       - Change effort
due [YYYY-MM-DD]     - Set due date
type add [type]      - Add type tag
field [name] [value] - Update any field
```

---

## Field Inference Guide

| Field | Look For |
|-------|----------|
| priority | "urgent/critical/emergency" → critical; "important/ASAP" → high; "nice to have" → low |
| status | "working on" → active; "blocked by" → blocked; "waiting for" → waiting |
| phase | "scoping" → planning; "building" → executing; "testing" → testing; "wrapping up" → closing |
| effort | "quick fix" → simple; "complex/involved" → complex; default → moderate |

---

## Draft Example

```
🕐 Current Date/Time: December 18, 2025 at 14:30 PST
Creating new task...

TASK DRAFT
═══════════════════════════════════════
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
═══════════════════════════════════════

Give me the priority and status values for this new task.
Also, which track does this belong to?
```

Output varies by file_operations setting. See `cmd-output-behavior.md`.

**Complete:** Per `cmd-shared-patterns.md`

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No task file uploaded (!task-e) | Ask user to upload the task file |
| Can't infer title (!task-c) | Ask user to describe the task |
| Invalid priority/status | Show valid options, stay in edit loop |
| Missing required field on done | List missing fields, stay in edit loop |

Common errors: See `cmd-shared-patterns.md`

---

## Integration

| Works With | Notes |
|------------|-------|
| !hi-[target] | Uses stored context for track inference |
| Obsidian Tasks | Tasks visible via plugin |
| Dataview | Query tasks by frontmatter fields |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.9.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.8.0 | 2025-12-19 | Reset to pre-release versioning |
| 5.0 | 2025-12-18 | Added user-prefs support |
