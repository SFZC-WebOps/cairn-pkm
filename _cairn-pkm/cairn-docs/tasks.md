# Tasks

Individual task files within tracks.

---

## Overview

Tasks are markdown files in `{track}/tasks/`.

Each task is a separate file with:
- YAML frontmatter for metadata
- Body with history, subtasks, notes

---

## Location

```
Tracks/{track}/tasks/
├── 20251218-fix-ssl-cert.md
├── 20251217-review-budget.md
└── 20251215-update-docs.md
```

---

## Filename

**Format:** `YYYYMMDD-slug.md`

- Date of creation
- 3-4 word slug
- Lowercase, hyphenated
- Max 40 characters for slug

**Examples:**
- `20251218-fix-ssl-cert.md`
- `20251218-review-q4-budget.md`
- `20251218-update-user-docs.md`

---

## Task File Structure

```markdown
---
title: "Task title"
project: p###-xxxx-yyyy
created_date: YYYY-MM-DD
due_date: YYYY-MM-DD
assignee: Name
priority: medium
status: active
phase: executing
effort: moderate
viz: eyeson
type:
  - category/subcategory
parent_task: parent-filename.md
last_update:
  - "YYYY-MM-DD: Latest update"
---

## Task History

- YYYY-MM-DD: Created task - brief summary
- YYYY-MM-DD: Status update - what changed

---

## Subtasks

- [ ] First subtask
- [ ] Second subtask
- [x] Completed subtask

---

## Notes

Working notes, context, links.

---
```

---

## Frontmatter Fields

### Required

| Field | Type | Description |
|-------|------|-------------|
| title | string | Task name |
| project | string | Track identifier |
| created_date | date | YYYY-MM-DD |
| priority | enum | Urgency level |
| status | enum | Current state |

### Optional

| Field | Type | Description |
|-------|------|-------------|
| due_date | date | When it's due |
| assignee | string | Who's responsible |
| phase | enum | Work phase |
| effort | enum | Complexity |
| viz | string | Dashboard visibility |
| type | array | Categories |
| parent_task | string | Parent filename |
| last_update | array | Recent history |

---

## Field Values

### priority

| Value | Use for |
|-------|---------|
| low | Nice to have, backlog |
| medium | Normal work |
| high | Important, ASAP |
| critical | Urgent, emergency |

### status

| Value | Use for |
|-------|---------|
| active | Currently working on |
| blocked | Waiting on dependency |
| waiting | Delegated, awaiting response |
| scheduled | Planned for specific time |
| deferred | On hold |
| onhold | Paused indefinitely |
| complete | Done |

### phase

| Value | Use for |
|-------|---------|
| planning | Scoping, requirements |
| executing | Building, implementing |
| testing | Verifying, QA |
| closing | Wrapping up |

### effort

| Value | Use for |
|-------|---------|
| simple | Quick fix, < 1 hour |
| moderate | Standard, 1-4 hours |
| complex | Involved, > 4 hours |

### viz

Dashboard visibility for filtering:

| Value | Meaning |
|-------|---------|
| 11 | Highest priority, must see today |
| eyeson | Active attention needed |
| reviewing | Under review |
| scheduled | Planned for future |
| backlog | Waiting in queue |
| system | Maintenance tasks |

---

## Body Sections

### Task History

Chronological record of updates:

```markdown
## Task History

- 2025-12-18: Created task - initial setup needed
- 2025-12-19: Started implementation - 50% complete
- 2025-12-20: Completed - deployed to production
```

**Format:** `YYYY-MM-DD: Action - brief summary`

**Rules:**
- One line per entry
- Under 200 characters
- Past tense
- Outcome-focused

### Subtasks

Checkboxes for sub-items:

```markdown
## Subtasks

- [x] Research options
- [x] Draft proposal
- [ ] Get approval
- [ ] Implement solution
- [ ] Verify results
```

**Features:**
- Standard markdown checkboxes
- Works with Obsidian Tasks plugin
- Displayed in dashboard views
- Progress tracked (N/M complete)

### Notes

Free-form working area:

```markdown
## Notes

Context: This relates to the Q4 security audit.

Links:
- [[vendor-acme-hosting]]
- External docs: https://example.com/docs

Questions:
- Should we use approach A or B?
- Who needs to approve?
```

---

## Parent-Child Relationships

### Creating Subtask Files

For complex subtasks, create separate files:

**Parent task:** `20251218-migration-project.md`
```yaml
title: "Database Migration Project"
```

**Child task:** `20251219-backup-database.md`
```yaml
title: "Backup Database"
parent_task: 20251218-migration-project.md
```

### Display

- Parent tasks show child count
- Children indent under parents in views
- Orphaned children (missing parent) show warning

---

## Working with Tasks

### Creating

1. Use [task template](templates/task.md)
2. Or use `!task-c` command
3. Or create manually

### Updating

1. Edit file directly
2. Or use `!task-e` command
3. Add history entry for significant changes

### Completing

1. Change `status: complete`
2. Add final history entry
3. Optionally move to `zzz/` archive

---

## Querying Tasks

### Dataview Examples

All active tasks:
```dataview
TABLE status, priority, due_date
FROM "Tracks"
WHERE contains(file.folder, "tasks")
AND status != "complete"
```

Overdue tasks:
```dataview
TASK FROM "Tracks"
WHERE !completed AND due < date(today)
```

Tasks by project:
```dataview
TABLE title, status
FROM "Tracks/p042-blog-migr/tasks"
WHERE status != "complete"
```

---

## Commands

- [!task-c](commands/cmd-task.md) — Create new task
- [!task-e](commands/cmd-task.md) — Edit existing task
- [!hi](commands/cmd-hi.md) — View track with tasks
- [!bye](commands/cmd-bye.md) — Generate task history entries

---

*Back to [Folders](folders.md) | [Home Docs](home-docs.md) | [README](README.md)*
