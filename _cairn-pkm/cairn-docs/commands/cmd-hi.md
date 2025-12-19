# !hi — Work Overview

**Type:** Read | **Version:** 11.1

Get a comprehensive overview of a project or area with all active tasks.

---

## Quick Reference

| Command | Purpose | Output |
|---------|---------|--------|
| `!hi-p14` | Project overview | Project details + tasks |
| `!hi-area-finance` | Area overview | Area details + tasks |

---

## What It Does

1. Displays current date/time
2. Reads track home doc (`_{track}-home.md`)
3. Discovers all task files in `tasks/` folder
4. Builds task hierarchy (parent-child relationships)
5. Sets context for `!bye` command
6. Displays comprehensive report

---

## Output Format

```markdown
## [target] Overview
**Status:** [status] | **Progress:** [progress]% | **Priority:** [priority]

### Summary
[From home doc]

### Current Status
[Status notes]

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

---
✅ Context set to [target] for !bye command
```

---

## Task File Parsing

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
| viz | string | Dashboard visibility |
| parent_task | string | Filename of parent |

### Inline Subtasks

Body checkboxes in the Subtasks section:
```markdown
## Subtasks
- [ ] First subtask
- [x] Completed subtask
```

---

## Context Persistence

- **Set by:** `!hi-[target]`
- **Used by:** `!bye` for log entry location
- **Override:** Natural language always wins

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Home doc missing | "⚠️ Target not found. Check track name." STOP |
| Log section missing | "⚠️ No log section found" CONTINUE |
| No task files found | "No active tasks found" CONTINUE |
| Task file parse error | "⚠️ Could not parse: [filename]" CONTINUE |

---

## Performance

| Track Size | Expected Time |
|------------|---------------|
| Small (<10 tasks) | ~1-2 seconds |
| Medium (10-30 tasks) | ~2-3 seconds |
| Large (30+ tasks) | ~3-4 seconds |

---

*Back to [Commands Index](index.md) | See also: [!bye](cmd-bye.md)*
