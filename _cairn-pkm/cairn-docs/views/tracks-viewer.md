# tracks-viewer View

Browse and explore all projects and areas.

---

## Location

`_cairn-pkm/views/tracks-viewer.md`

---

## Features

### Project Selector

Dropdown menu to select:
- Any project (`p###-xxxx-yyyy`)
- Any area (`area-xxx`)
- System

Selection persists via sessionStorage.

### Project Display

For selected project shows:
- Title and status
- Summary (from home doc)
- Task count
- Tasks grouped by section

### Task Cards

Each task displays:
- Title with link
- Status, priority, viz badges
- Assignee and due date
- Task history
- Body subtasks

### Parent-Child Hierarchy

- Child tasks nested under parents
- Orphan warnings displayed
- Indentation shows hierarchy

---

## Query Logic

```javascript
// Load all home docs
const trackHomeDocs = dv.pages('"Tracks"')
  .where(p => p.file.name.endsWith('-home'));

// Filter tasks by selected project
const filteredTasks = allTasks.filter(p => {
  const taskProject = clean(p.project).toLowerCase();
  return taskProject === selectedProject.toLowerCase();
});
```

---

*Back to [Views Index](index.md)*
