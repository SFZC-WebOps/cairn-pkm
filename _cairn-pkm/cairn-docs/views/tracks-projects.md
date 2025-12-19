# tracks-projects View

Overview of all active projects.

---

## Location

`_cairn-pkm/views/tracks-projects.md`

---

## Sections

### Projects Table

Active projects with:
- Project link
- Status
- Progress %
- Open task count
- Done task count
- Last updated

```dataview
TABLE
  status, progress + "%", 
  length(filter(file.tasks, (t) => !t.completed)) as "Open",
  length(filter(file.tasks, (t) => t.completed)) as "Done"
FROM "Tracks"
WHERE contains(file.name, "p0") AND status = "active"
```

### Project Health

Health indicators per project:
- 🟢 Good — Normal task count
- ⚪ No tasks — Empty project
- ✅ Complete? — All tasks done
- 🟡 Busy — 10+ open tasks
- 🔴 Heavy — 20+ open tasks

### Recently Updated

Last 5 updated projects.

### Stale Projects

Projects not updated in 14+ days.

---

## Use Cases

- Project portfolio review
- Identifying neglected projects
- Progress tracking

---

*Back to [Views Index](index.md)*
