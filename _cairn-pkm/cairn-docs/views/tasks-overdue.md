# tasks-overdue View

Focus on time-sensitive tasks.

---

## Location

`_cairn-pkm/views/tasks-overdue.md`

---

## Sections

### Overdue

Tasks past their due date.

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due AND due < date(today)
SORT due ASC
GROUP BY file.folder
```

### Due Today

Tasks due today.

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due = date(today)
SORT file.name ASC
```

### Due This Week

Tasks due in next 7 days.

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due > date(today) AND due <= date(today) + dur(7 days)
SORT due ASC
GROUP BY file.folder
```

### Summary

Count display:
- 🔴 Overdue count
- 🟡 Due today count
- 🟢 Due this week count

---

## Use Cases

- Daily review of urgent items
- Weekly planning
- Identifying bottlenecks

---

*Back to [Views Index](index.md)*
