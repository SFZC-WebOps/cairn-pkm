# !task — Task Management

**Type:** Display | **Version:** 4.2

Create and edit task files through interactive conversation.

---

## Quick Reference

| Command | Purpose | Permission |
|---------|---------|------------|
| `!task-c` | Generate new task from conversation | Display only |
| `!task-e` | Edit existing task (upload first) | Display only |

---

## !task-c — Create Task

### Workflow

1. Discuss work needed in conversation
2. Type `!task-c`
3. Review draft with inferred fields
4. Provide required fields (priority, status)
5. Use interactive commands to adjust
6. Type `done` to get complete file
7. Copy and save locally

### Required Fields

| Field | Values |
|-------|--------|
| title | Task name |
| project | Track identifier |
| created_date | Auto-set to today |
| priority | low, medium, high, critical |
| status | active, blocked, complete, deferred, onhold, scheduled, waiting |

### Optional Fields

| Field | Values |
|-------|--------|
| due_date | YYYY-MM-DD |
| assignee | Person name |
| phase | planning, executing, testing, closing |
| effort | simple, moderate, complex |
| parent_task | Filename of parent task |

### Interactive Commands

```
done                 # Display final file
edit [field]         # Modify any field
status [value]       # Set status
priority [value]     # Set priority
phase [value]        # Set phase
effort [value]       # Set effort
type add [type]      # Add type tag
type remove [type]   # Remove type tag
```

### Output Example

```
Current Date/Time: December 16, 2025 at 14:30 PST
Creating new task...

TASK DRAFT
═══════════════════════════════════════════════════════
Filename: 20251216-ssl-cert-chain-validation.md

title: Fix SSL certificate chain validation
project: [need track - which project/area?]
type:
  - webops/security
  - webops/infrastructure

Subtasks detected (2):
  - Check certificate chain
  - Update intermediate certificate
═══════════════════════════════════════════════════════

Give me the priority and status values:
- priority: low, medium, high, critical
- status: active, blocked, complete, deferred, onhold, scheduled, waiting
```

---

## !task-e — Edit Task

### Workflow

1. Upload existing task file
2. Discuss updates in conversation
3. Type `!task-e`
4. Review proposed changes
5. Use interactive commands to adjust
6. Type `done` to get updated file
7. Save locally

### Interactive Commands

```
done                 # Display final file
edit                 # Modify history entry text
status [value]       # Change status
priority [value]     # Change priority
phase [value]        # Change phase
effort [value]       # Change effort
due [YYYY-MM-DD]     # Set due date
type add [type]      # Add type tag
field [name] [value] # Update any field
```

### History Entry Format

One line, under 200 characters, past tense, outcome-focused.

**Good:**
- `2025-11-08: Approved configuration change - implementation scheduled`
- `2025-11-04: Conducted test; identified tracking failure`

**Bad:**
- `Working on stuff` (vague)
- `I updated the configuration` (verbose)

---

## Task File Template

```yaml
---
title: "Task title"
project: "p###-xxxx-yyyy"
created_date: YYYY-MM-DD
due_date: 
assignee: 
priority: medium
status: active
phase: 
effort: 
type:
  - category/subcategory
last_update:
  - "YYYY-MM-DD: Created task - brief summary"
---

### Task History

- YYYY-MM-DD: Created task - brief summary

---

### Subtasks

- [ ] First subtask
- [ ] Second subtask

---

### Notes


---
```

---

## Field Inference

| Field | Look For |
|-------|----------|
| priority | "urgent/critical" → critical; "important" → high; "backlog" → low |
| status | "working on" → active; "blocked by" → blocked; "waiting for" → waiting |
| phase | "scoping" → planning; "building" → executing; "testing" → testing |
| effort | "quick fix" → simple; "complex" → complex |

---

## Filename Generation

Format: `YYYYMMDD-slug.md`

From title, extract 3-4 key words, lowercase, hyphenated, max 40 chars.

| Title | Slug |
|-------|------|
| "Fix SSL certificate chain validation" | `ssl-cert-chain-validation` |
| "Set up new environment" | `setup-new-environment` |

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No task file uploaded (!task-e) | Ask user to upload |
| Can't infer title (!task-c) | Ask user to describe |
| Invalid priority/status | Show valid options, stay in loop |
| Missing required on done | List missing, stay in loop |

---

*Back to [Commands Index](index.md)*
