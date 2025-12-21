# !hi - Work Session
*Type: Interactive | Updated: 2025-12-20*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!hi` | Open main menu |
| `!hi-[target]` | Display track overview with tasks |

**Session model:** `!hi` opens → work via commands → `!bye` closes with logging

---

## Initialization

Per `cmd-shared-patterns.md`

---

## !hi — Main Menu

```
🧭 What would you like to do?

1. Open a project or area
2. Create something new
3. Edit something
4. Quick note
5. Record skill evidence
6. Close session

Enter number or describe what you need:
```

### Input Handling

| Input | Action |
|-------|--------|
| Number (1-6) | Route to selection |
| Direct command (`!create task`) | Execute command |
| Natural language | Interpret and route |
| Question | Answer using context |

### Routing

| # | Action |
|---|--------|
| 1 | "Which track?" → `!hi-[target]` |
| 2 | "Create what? (area \| project \| task \| object [type])" → `!create [type]` |
| 3 | "Edit what? (filename or upload file)" → `!edit [filename]` |
| 4 | `!quicknote` flow |
| 5 | `!skills` flow |
| 6 | `!bye` flow |

---

## !hi-[target] — Track Overview

### Display Format

```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
READ: All task files in [target]/tasks/
PARSE: Task hierarchy (parent-child relationships)
FILTER: status != complete (unless showing archive)
SORT: priority (critical → low), then created_date
```

**Output:**

```markdown
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Type:** [area|project]

### Summary
[Overview section from home doc]

### Current Focus
[Current Focus section from home doc]

### Recent Activity (Last 10-15 entries)
[Log entries with timestamps]

### Active Tasks ([N] tasks, [X] overdue, [Y] due this week)

┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 [title]
Status: [status] | Priority: [priority] | Viz: [viz] | Effort: [effort]
Phase: [phase] | Assignee: [assignee]
Created: [created_date] | Due: [due_date] ⚠️ OVERDUE

Inline Subtasks (N):
  └─ [x] Completed subtask
  └─ [ ] Pending subtask (due YYYY-MM-DD)

Subtask Files (N):
  → Child task title (status, due date)
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Repeat for each task]

### Task Summary

**By Status:**
- Active: N
- Blocked: N
- Waiting: N

**By Priority:**
- Critical: N
- High: N
- Medium: N
- Low: N

**Time Alerts:**
- 🔴 Overdue: N tasks/subtasks
- 📅 Due This Week: N tasks/subtasks

═══════════════════════════════════════════════
✅ Context set to [target] for !bye command
═══════════════════════════════════════════════

🧭 What next?

1. Create a task → !create task
2. Edit a task → !edit [filename]
3. Add log entry → !edit [track-home]
4. Update status/progress → !edit [track-home]
5. Switch track → !hi-[other-track]
6. Close session → !bye
```

### Interactive Menu (Optional)

After displaying overview, show mini-menu for common actions. User can:
- Type number for quick action
- Enter command directly
- Type freeform request

---

## Task Parsing

### Required Frontmatter

| Field | Type | Values |
|-------|------|--------|
| title | string | Task name |
| project | string | Track identifier |
| created_date | date | YYYY-MM-DD |
| status | enum | active, blocked, complete, deferred, onhold, scheduled, waiting |
| priority | enum | low, medium, high, critical |

### Optional Frontmatter

| Field | Type | Description |
|-------|------|-------------|
| due_date | date | YYYY-MM-DD |
| assignee | string | Person responsible |
| phase | enum | planning, executing, testing, closing |
| effort | enum | simple, moderate, complex |
| viz | string | Dashboard visibility (now, next, soon, later, blocked, waiting) |
| parent_task | string | Filename of parent |

### Inline Subtasks

Body checkboxes in Subtasks section:
```markdown
### Subtasks
- [ ] First subtask
- [x] Completed subtask
```

### Parent-Child Relationships

- Parent task: Higher-level work item
- Child tasks: Implementation steps linked via `parent_task` field
- Display shows hierarchy with indentation/arrows

---

## Context Persistence

```
SET on !hi-[target]: 
  - session_active = true
  - current_track = [target]
  
TRACK during session:
  - tracks_viewed[]
  - files_created[] (via !create)
  - files_edited[] (via !edit)
  - decisions[]
  - commands_used[]

USE on !bye: 
  - Full context for log generation
  - Primary track = current_track
  
CLEAR on !bye: 
  - Session state reset
```

---

## Freeform Input Examples

**Main menu:**
- "Create a task for the storage migration" → Prompts for track, then `!create task`
- "What projects are active?" → Lists active projects from Tracks/
- "Show me p14" → `!hi-p014` (with correction if needed)
- "Edit my finance area" → `!edit _area-finance-home.md`

**After track overview:**
- "Mark the SSL task complete" → `!edit 20251220-ssl-cert.md` with status suggestion
- "Add a note that we're waiting on vendor" → `!edit _[track]-home.md` with log entry suggestion
- "Create a task to follow up next week" → `!create task` with track pre-filled

---

## Command Integration

| When User Says... | Route To | With Context |
|-------------------|----------|--------------|
| Create area/project/task/object | !create [type] | Track from session if applicable |
| Edit [filename] | !edit [filename] | Reads from vault |
| Add log entry | !edit [track-home] | Suggests log entry command |
| Update status | !edit [track-home] | Suggests status update |
| Quick note | !quicknote | Conversation context |
| Record skill | !skills | Session context |

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Ambiguous input | Ask clarifying question |
| Track not found | "Track not found. Available: [list]" |
| Can't interpret request | "I didn't understand. Try a number or command?" |
| No tasks found | "No active tasks in [track]" CONTINUE |
| Task file parse error | "⚠️ Could not parse: [filename]" CONTINUE |
| Home doc missing | "⚠️ Track home doc not found. Check track name." STOP |

---

## Performance Notes

| Track Size | Expected Time |
|------------|---------------|
| Small (<10 tasks) | ~1-2 seconds |
| Medium (10-30 tasks) | ~2-3 seconds |
| Large (30+ tasks) | ~3-4 seconds |

For very large tracks (50+ tasks), consider filtering by viz field or status for faster display.