# task Template

Create a task file in a track.

---

## Prompts

| Prompt | Purpose | Required |
|--------|---------|----------|
| Task title | What the task is | Yes |
| Track | Where it belongs (auto-detected if in track folder) | Yes |
| Priority | low, medium, high, critical | Yes |
| Status | active, blocked, waiting, scheduled, deferred | Yes |
| Due date | YYYY-MM-DD | No |

---

## Output

**Filename:** `YYYYMMDD-slug.md`

**Location:** `Tracks/{track}/tasks/`

---

## Generated File

```markdown
---
title: "{title}"
project: {track}
created_date: YYYY-MM-DD
due_date: {due_date}
assignee: 
priority: {priority}
status: {status}
phase: 
effort: 
---

## Task History

- YYYY-MM-DD: Created task

---

## Subtasks

- [ ] 

---

## Notes


```

---

## Field Reference

### Priority

| Value | Use for |
|-------|---------|
| low | Nice to have, backlog |
| medium | Normal work |
| high | Important, ASAP |
| critical | Urgent, emergency |

### Status

| Value | Use for |
|-------|---------|
| active | Currently working on |
| blocked | Waiting on external dependency |
| waiting | Delegated, awaiting response |
| scheduled | Planned for specific time |
| deferred | On hold, deprioritized |
| complete | Done |

### Phase

| Value | Use for |
|-------|---------|
| planning | Scoping, requirements |
| executing | Building, implementing |
| testing | Verifying, QA |
| closing | Wrapping up |

### Effort

| Value | Use for |
|-------|---------|
| simple | Quick fix, < 1 hour |
| moderate | Standard work, 1-4 hours |
| complex | Involved, > 4 hours |

---

*Back to [Templates Index](index.md)*
