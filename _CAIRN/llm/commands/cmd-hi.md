# !hi - Work Overview
*Type: Read | Version: 11.1 | Updated: 2025-12-16*

## Quick Reference

| Command | Purpose | Output | Context Set |
|---------|---------|--------|-------------|
| `!hi-p14` | Deep dive into specific project | Project details + tasks | Yes (for !bye) |
| `!hi-[area]` | Deep dive into area | Area details + tasks | Yes (for !bye) |

**Task System:** Reads from track `tasks/` folders
**Execution Time:** 2-4 seconds per command

---

## Requirements

**Assistant Capabilities:**
- File system read access
- Directory listing
- YAML frontmatter parsing
- Date calculations (overdue, due this week)

**Vault Structure:**
- `Tracks/` — Projects and areas
- `Tracks/*/tasks/` — Task files per track
- `Tracks/*/_*-home.md` — Home docs with status and log

---

## Execution

### Sequence

1. Display: `📅 Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}`
2. Parse target: `p###-xxxx-yyyy` = project, `area-*` = area
3. Determine target path: `{VAULT_PATH}/Tracks/[target]/`
4. Read `[target]/_*-home.md` — extract YAML fields and log section
5. Discover all task files in `[target]/tasks/`
6. Parse task frontmatter and build task hierarchy
7. SET_CONTEXT: target_identifier (persists for !bye)
8. Display comprehensive report

### Task Discovery

```
SCAN: {VAULT_PATH}/Tracks/[target]/tasks/
MATCH: *.md files
READ: Each task file's frontmatter
FILTER: status != complete
SORT: By created_date (newest first)
```

### Task Hierarchy Building

```
FOR EACH task file:
  READ frontmatter:
    - title, status, priority, viz, due_date, assignee
    - created_date, phase, effort, type
    - parent_task, subtasks[]
  
  IF parent_task is empty:
    Add to top_level_tasks[]
    
    IF subtasks[] has entries:
      FOR EACH inline subtask:
        Parse: text, status, due_date, assignee, note
        Add to task.inline_subtasks[]
  
  ELSE:
    Add to child_tasks[] with parent reference

BUILD HIERARCHY:
  FOR EACH top_level_task:
    Find child tasks where parent_task == this task's filename
    Attach as task.separate_subtasks[]
```

### Checkbox Logic

```
Inline subtasks:
  [x] if status == "complete"
  [ ] otherwise
```

### Overdue Indicator

```
Show ⚠️ OVERDUE when:
  due_date < today AND status != "complete"
  
Apply to:
  - Parent tasks with due_date
  - Inline subtasks with due_date
  - Separate subtask files with due_date
```

### Due This Week Calculation

```
Count tasks/subtasks where:
  due_date >= today AND due_date <= today + 7 days
  AND status != "complete"
```

---

## Output Format

```markdown
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Priority:** [priority]

### Summary
[summary from home doc OR "No summary available"]

### Current Status
[status notes from home doc]

### Recent Activity (Last 10-15 entries)
[Log entries with timestamps]

### Active Tasks ([N] tasks, [X] overdue, [Y] due this week)

┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 [title]
Status: [status] | Priority: [priority] | Viz: [viz] | Effort: [effort]
Phase: [phase] | Assignee: [assignee]
Created: [created_date] | Due: [due_date OR "(none)"] [⚠️ OVERDUE if applicable]

[IF inline_subtasks exists:]
Inline Subtasks ([count]):
  └─ [[x] OR [ ]] [text] ([status][, due [due_date]][⚠️ OVERDUE if applicable])
     [IF note exists:] Note: [note]

[IF separate_subtasks exists:]
Subtask Files ([count]):
  → [task title] ([status][, due [due_date]][⚠️ OVERDUE if applicable])
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Repeat for each task]

---

### Task Summary

**By Status:**
- Active: [count]
- Blocked: [count]
- Waiting: [count]
- Scheduled: [count]
- On Hold: [count]
- Deferred: [count]

**By Priority:**
- Critical: [count]
- High: [count]
- Medium: [count]
- Low: [count]

**Time Alerts:**
- 🔴 Overdue: [count] tasks/subtasks
- 📅 Due This Week: [count] tasks/subtasks

---
✅ Context set to [target] for !bye command
```

---

## Task File Parsing

**Required Frontmatter:**
- title (string)
- project (string, matches track identifier)
- created_date (YYYY-MM-DD)
- status (active|blocked|complete|deferred|onhold|scheduled|waiting)
- priority (low|medium|high|critical)

**Optional Frontmatter:**
- due_date (YYYY-MM-DD or empty)
- assignee (string)
- phase (planning|executing|testing|closing)
- effort (simple|moderate|complex)
- viz (for visibility/review categorization)
- parent_task (filename without path)
- subtasks (array of objects)
- type (array of strings)

**Inline Subtask Object:**
```yaml
subtasks:
  - text: "Subtask description"
    status: active|complete|waiting|...
    due_date: YYYY-MM-DD or empty
    assignee: "Name"
    note: "Optional note"
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Home doc missing | "⚠️ Target not found. Check track name." STOP |
| Log section missing | "⚠️ No log section found" CONTINUE |
| No task files found | "No active tasks found for this track." CONTINUE |
| Task file parse error | "⚠️ Could not parse task file: [filename]" CONTINUE |

---

## Context Persistence

```
SET on !hi-[target]: context = target_identifier
USE on !bye: target_context for log entry
OVERRIDE: Natural language always wins
```

---

## File Paths

| Purpose | Path |
|---------|------|
| Base | `{VAULT_PATH}/` |
| Tracks | `{VAULT_PATH}/Tracks/` |
| Tasks | `{VAULT_PATH}/Tracks/[target]/tasks/` |
| Task pattern | `YYYYMMDD-slug.md` |

---

## Performance

| Track Size | Expected Time |
|------------|---------------|
| Small (<10 tasks) | ~1-2 seconds |
| Medium (10-30 tasks) | ~2-3 seconds |
| Large (30+ tasks) | ~3-4 seconds |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 9.0 | 2025-10-02 | CSV-based integration |
| 10.0 | 2025-12-01 | Migrated to markdown task system |
| 11.0 | 2025-12-15 | LLM-agnostic refactor |
| 11.1 | 2025-12-16 | Standardized format |
