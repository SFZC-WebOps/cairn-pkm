# tasks-dashboard View

Main task management dashboard with advanced features.

---

## Location

`_cairn-pkm/views/tasks-dashboard.md`

---

## Features

### Grouping

Tasks are grouped by:
1. **Project** — All tasks for each project together
2. **Section** — In Progress, Review, Blocked, Backlog
3. **Viz tag** — Priority within sections

### Viz Tags

| Viz | Meaning | Display |
|-----|---------|---------|
| `11` | Highest priority | Red accent, no indent |
| `eyeson` | Active attention | Orange accent |
| `reviewing` | Under review | |
| `scheduled` | Planned | |
| `backlog` | Waiting | |
| `system` | Maintenance | |

### Filter Toggle

Checkbox at top of view:
- **Unchecked:** Shows only `viz: 11` and `viz: eyeson`
- **Checked:** Shows all viz tags

### Collapsible Sections

- Each project is collapsible
- State persists via localStorage
- Expand All / Collapse All buttons
- Red dot indicator when project has `viz: 11` tasks

### Task Cards

Each task shows:
- Title (linked to file)
- Status badge
- Priority badge
- Phase and effort
- Assignee and due date
- Days until/overdue
- Task history (last 3 entries)
- Subtask progress

### Parent-Child Relationships

- Child tasks indent under parents
- Orphaned children (missing parent) show warning
- Hierarchical display

### Subtasks

Body checkboxes displayed:
- Progress counter (N/M complete)
- Individual checkbox status
- Link to edit

---

## Visual Design

### Card Colors

Background gradients based on viz:
- `11`: Pink/red
- `eyeson`: Yellow/orange
- `backlog`: Teal
- `scheduled`: Purple
- `system`: Gray
- `reviewing`: Orange

### Border Colors

Left border accent matches viz priority.

### Badges

Status, priority, phase, effort all have distinct badge styles.

---

## Query Logic

```javascript
// Filter tasks by viz
.where(p => {
  if (showAllViz) {
    return viz !== null && viz !== undefined;
  } else {
    return viz === 11 || viz === "eyeson";
  }
})

// Group by project
.groupBy(t => t.project)

// Sort sections by priority
["In Progress", "Review", "Blocked", "Backlog"]

// Sort viz within sections
[11, "eyeson", "reviewing", "scheduled", "backlog", "system"]
```

---

## Customization

To modify:
1. Copy to `_local/views/`
2. Adjust filter logic
3. Change color schemes
4. Modify card layout

---

*Back to [Views Index](index.md)*
