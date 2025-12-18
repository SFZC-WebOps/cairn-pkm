# Overdue Tasks

Tasks past their due date across all tracks.

## Overdue

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due AND due < date(today)
SORT due ASC
GROUP BY file.folder
```

## Due Today

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due = date(today)
SORT file.name ASC
```

## Due This Week

```dataview
TASK
FROM "Tracks"
WHERE !completed AND due > date(today) AND due <= date(today) + dur(7 days)
SORT due ASC
GROUP BY file.folder
```

## Summary

```dataviewjs
const tasks = dv.pages('"Tracks"').file.tasks;
const overdue = tasks.where(t => !t.completed && t.due && t.due < dv.date("today")).length;
const today = tasks.where(t => !t.completed && t.due && t.due.equals(dv.date("today"))).length;
const week = tasks.where(t => !t.completed && t.due && t.due > dv.date("today") && t.due <= dv.date("today").plus({days: 7})).length;

dv.paragraph(`🔴 **${overdue}** overdue`);
dv.paragraph(`🟡 **${today}** due today`);
dv.paragraph(`🟢 **${week}** due this week`);
```
