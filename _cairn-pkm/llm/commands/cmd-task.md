# !task - Task Management
*Type: Display | Version: 4.2 | Updated: 2025-12-16*

## Quick Reference

| Command | Purpose | Permission |
|---------|---------|------------|
| `!task-c` | Generate new task file from conversation | Display only |
| `!task-e` | Generate edited task from uploaded file | Display only |

**Location:** `{VAULT_PATH}/Tracks/[track]/tasks/`

**Workflow:**
- **Create:** Discuss work → `!task-c` → Review draft → Provide required fields → `done` → Copy file locally
- **Edit:** Upload task file → Discuss updates → `!task-e` → Review changes → `done` → Update file locally

---

## Requirements

**Assistant Capabilities:**
- Conversation context analysis
- YAML frontmatter generation
- Timestamp generation
- Interactive editing loop

**Vault Structure:**
- `Tracks/[track]/tasks/` — Task files per track

---

## !task-c — Create Task

### Execution

1. Display current date/time
2. Infer track context (from !hi, conversation, or ask user)
3. Extract from conversation: title, types, and any mentioned fields
4. Generate filename: `{YYYYMMDD}-{slug}.md`
5. Show draft, prompt for required fields (priority, status)
6. Interactive edit loop until user types `done`
7. Display complete file content for user to create locally

### Required Fields

| Field | Description |
|-------|-------------|
| title | Task name (extracted or provided) |
| project | Track identifier |
| created_date | Auto-set to today |
| priority | `low \| medium \| high \| critical` |
| status | `active \| blocked \| complete \| deferred \| onhold \| scheduled \| waiting` |

### Optional Fields

| Field | Description |
|-------|-------------|
| due_date | YYYY-MM-DD |
| assignee | Default assignee name |
| phase | `planning \| executing \| testing \| closing` |
| effort | `simple \| moderate \| complex` |
| type | Array of category/subcategory |
| parent_task | Filename of parent task |

### Subtask Handling

Subtasks are **body content only** — not stored in frontmatter.

Assistant scans conversation for task breakdowns:
- "Steps are: X, Y, Z"
- "Need to: first X, then Y, then Z"
- Numbered or bulleted lists describing work items

Each extracted subtask becomes a checkbox in the body's Subtasks section.

### Interactive Commands

```
done                 - Display final file content
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
assignee: {default_assignee}
parent_task: 
priority: {required}
status: {required}
phase: 
effort: 
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

---

## !task-e — Edit Task

### Execution

1. Display current date/time
2. Read uploaded task file
3. Generate history entry from conversation context
4. Interactive edit loop until user types `done`
5. Display complete edited file for user to save locally

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
done                 - Display final edited file
edit                 - Modify history entry text
status [value]       - active, blocked, complete, deferred, onhold, scheduled, waiting
priority [value]     - low, medium, high, critical
phase [value]        - planning, executing, testing, closing
effort [value]       - simple, moderate, complex
due [YYYY-MM-DD]     - Set due date
type add [type]      - Add type tag
field [name] [value] - Update any field
```

---

## Field Inference Guide

| Field | Look For |
|-------|----------|
| priority | "urgent/critical/emergency" → critical; "important/ASAP" → high; "nice to have/backlog" → low |
| status | "working on" → active; "blocked by" → blocked; "waiting for" → waiting; "planned for" → scheduled |
| phase | "scoping/planning" → planning; "building/implementing" → executing; "testing/verifying" → testing; "wrapping up" → closing |
| effort | "quick fix" → simple; "complex/involved" → complex; default → moderate |
| type | Match technical domains mentioned in conversation |

---

## Output Format

### Create Example

```
Current Date/Time: December 16, 2025 at 14:30 PST
Creating new task...

TASK DRAFT
═══════════════════════════════════════
Filename: 20251216-wp-environment-setup.md

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

Give me the priority and status values for this new task:
- priority: low, medium, high, critical
- status: active, blocked, complete, deferred, onhold, scheduled, waiting

Also, which track does this belong to?
```

### Edit Example

```
Current Date/Time: December 16, 2025 at 15:00 PST

TASK UPDATE
═══════════════════════════════════════
Task: Path confirmation for Wave 1 migration
File: 20251102-path-confirmation.md

Proposed history entry:
2025-12-16: Completed path confirmation for all 20 Wave 1 folders
═══════════════════════════════════════

Commands: done | edit | status [value] | priority [value] | field [name] [value]
```

### Completion

```
COMPLETE FILE
═══════════════════════════════════════
Filename: {filename}
Path: {VAULT_PATH}/Tracks/[track]/tasks/

{complete file content with frontmatter and body}

═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No task file uploaded (!task-e) | Ask user to upload the task file |
| Can't infer title (!task-c) | Ask user to describe the task |
| Invalid priority/status | Show valid options, stay in edit loop |
| Missing required field on done | List missing fields, stay in edit loop |

---

## File Paths

| Purpose | Path |
|---------|------|
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
| 3.1 | 2025-11-28 | Previous version with frontmatter subtasks |
| 4.0 | 2025-12-15 | LLM-agnostic refactor |
| 4.1 | 2025-12-16 | Removed subtasks from frontmatter — body content only |
| 4.2 | 2025-12-16 | Standardized format |
