# !hi - Work Session
*Type: Interactive | Updated: 2025-12-22*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!hi` | Open main menu |
| `!hi-[target]` | Display track overview with tasks |

**Session model:** `!hi` opens â†’ work via commands â†’ `!bye` closes with logging

---

## Initialization

Per `cmd-shared-patterns.md`

---

## !hi â€” Main Menu

```
ðŸ§­ What would you like to do?

1. Open a project or area
2. Create something new
3. Edit something
4. Capture something
5. Record skill evidence
6. Close session

Enter number or describe what you need:

Type !help for command reference
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
| 1 | "Which track?" â†’ `!hi-[target]` |
| 2 | "Create what? (area \| project \| task \| object [type])" â†’ `!create [type]` |
| 3 | "Edit what? (filename or upload file)" â†’ `!edit [filename]` |
| 4 | `!capture` flow |
| 5 | `!skills` flow |
| 6 | `!bye` flow |

---

## !hi-[target] â€” Track Overview

### Display Format

```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
READ: All task files in [target]/tasks/
PARSE: Task hierarchy (parent-child relationships)
FILTER: status != complete (unless showing archive)
SORT: priority (critical â†’ low), then created_date
```

### Date Calculations

**Overdue detection:**
- Compare due_date < current_date in user's timezone
- Timezone from cairn-pkm-user-prefs.yaml

**Due this week:**
- due_date <= (current_date + 7 days)
- Calendar days, not business days

**Display:**
- âš ï¸ OVERDUE: Standard overdue (appears after due date with day count)
- âš ï¸ CRITICALLY OVERDUE (review needed): For tasks >90 days overdue
- ðŸ”´ Overdue: N tasks/subtasks (in summary)
- ðŸ“… Due This Week: N tasks/subtasks (in summary)

**Critical Overdue Threshold:**
When a task is more than 90 days overdue, display "âš ï¸ CRITICALLY OVERDUE (review needed)" instead of showing the day count. This highlights tasks that likely need urgent review or cleanup rather than just completion.

**Output:**

```markdown
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Type:** [area|project]

### Summary
[Overview section from home doc]

### Current Focus
[Current Focus section from home doc]

### Recent Activity

**Display Logic:**
- Show all log entries if fewer than 10 exist
- Show last 10-15 entries if 10 or more exist
- Most recent entries first (reverse chronological)

[Log entries with timestamps]

### Active Tasks ([N] tasks, [X] overdue, [Y] due this week)

â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”
ðŸ“‹ [title]
Status: [status] | Priority: [priority] | Viz: [viz] | Effort: [effort]
Phase: [phase] | Assignee: [assignee]
Created: [created_date] | Due: [due_date] âš ï¸ OVERDUE (45 days)
                                         OR
Created: [created_date] | Due: [due_date] âš ï¸ CRITICALLY OVERDUE (review needed)

Inline Subtasks (N):
  â”œâ”€ [x] Completed subtask
  â””â”€ [ ] Pending subtask (due YYYY-MM-DD)

Child Tasks (N):
  â†’ Child task title (status, priority, due: YYYY-MM-DD)
â”—â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”â”

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
- ðŸ”´ Overdue: N tasks/subtasks
- ðŸ“… Due This Week: N tasks/subtasks

â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
âœ… Context set to [target] for !bye command
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

ðŸ§­ What next?

1. Create a task â†’ !create task
2. Edit a task â†’ !edit [filename]
3. Add log entry â†’ !edit [track-home]
4. Update status/progress â†’ !edit [track-home]
5. Switch track â†’ !hi-[other-track]
6. Close session â†’ !bye
```

### Interactive Menu (Optional)

After displaying overview, show mini-menu for common actions. User can:
- Type number for quick action
- Enter command directly
- Type freeform request

### Progress Note

**Note on Progress:** The progress field in project/area frontmatter must be manually updated via `!edit`. It does not auto-calculate from task completion. Progress represents overall track completion, not just task counts.

To update: `!edit [track-home]` â†’ `progress [0-100]`

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
- Child tasks show: title, status, priority, due date (if set)

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
- "Create a task for the storage migration" â†’ Prompts for track, then `!create task`
- "What projects are active?" â†’ Lists active projects from Tracks/
- "Show me p14" â†’ `!hi-p014` (with correction if needed)
- "Edit my finance area" â†’ `!edit _area-finance-home.md`

**After track overview:**
- "Mark the SSL task complete" â†’ `!edit 20251220-ssl-cert.md` with status suggestion
- "Add a note that we're waiting on vendor" â†’ `!edit _[track]-home.md` with log entry suggestion
- "Create a task to follow up next week" â†’ `!create task` with track pre-filled

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
| Can't interpret request | "I didn't understand. Try !help for commands." |
| No tasks found | "No active tasks in [track]" CONTINUE |
| Task file parse error | "âš ï¸ Could not parse: [filename]" CONTINUE |
| Home doc missing | "âš ï¸ Track home doc not found. Check track name." STOP |

---

## Performance Notes

| Track Size | Expected Time |
|------------|---------------|
| Small (<10 tasks) | ~1-2 seconds |
| Medium (10-30 tasks) | ~2-3 seconds |
| Large (30+ tasks) | ~3-4 seconds |

For very large tracks (50+ tasks), consider filtering by viz field or status for faster display.
