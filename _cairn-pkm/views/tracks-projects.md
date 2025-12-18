# Active Projects

Overview of all active projects with task counts and status.

## Projects

```dataview
TABLE WITHOUT ID
  file.link as "Project",
  status as "Status",
  progress + "%" as "Progress",
  length(filter(file.tasks, (t) => !t.completed)) as "Open",
  length(filter(file.tasks, (t) => t.completed)) as "Done",
  file.mtime as "Updated"
FROM "Tracks"
WHERE contains(file.name, "p0") OR contains(file.name, "p1") OR contains(file.name, "p2")
WHERE status = "active"
SORT file.mtime DESC
```

## Project Health

```dataviewjs
const projects = dv.pages('"Tracks"')
    .where(p => p.file.name.match(/^_p\d{3}/) && p.status === "active");

if (projects.length === 0) {
    dv.paragraph("No active projects found.");
} else {
    const health = projects.map(p => {
        const open = p.file.tasks.where(t => !t.completed).length;
        const total = p.file.tasks.length;
        let status = "🟢 Good";
        if (open === 0 && total === 0) status = "⚪ No tasks";
        else if (open === 0) status = "✅ Complete?";
        else if (open > 20) status = "🔴 Heavy";
        else if (open > 10) status = "🟡 Busy";
        
        return [p.file.link, open, status];
    });
    
    dv.table(["Project", "Open Tasks", "Health"], health);
}
```

## Recently Updated

```dataview
TABLE WITHOUT ID
  file.link as "Project",
  file.mtime as "Last Updated"
FROM "Tracks"
WHERE contains(file.name, "p0") OR contains(file.name, "p1") OR contains(file.name, "p2")
SORT file.mtime DESC
LIMIT 5
```

## Stale Projects

Projects not updated in 14+ days:

```dataviewjs
const cutoff = dv.date("today").minus({days: 14});
const stale = dv.pages('"Tracks"')
    .where(p => p.file.name.match(/^_p\d{3}/) && p.status === "active")
    .where(p => dv.date(p.file.mtime) < cutoff);

if (stale.length === 0) {
    dv.paragraph("✅ All projects recently updated.");
} else {
    dv.table(
        ["Project", "Last Updated", "Days Stale"],
        stale.map(p => {
            const days = Math.floor((new Date() - new Date(p.file.mtime)) / (1000*60*60*24));
            return [p.file.link, p.file.mtime, days];
        })
    );
}
```
