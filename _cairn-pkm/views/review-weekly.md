# Weekly Dashboard

Combined view for weekly reviews and daily check-ins.

---

## 🚨 Attention Needed

### Overdue Tasks
```dataview
TASK
FROM "Tracks"
WHERE !completed AND due AND due < date(today)
SORT due ASC
LIMIT 10
```

### Inbox Items
```dataviewjs
const items = dv.pages('"Capture"').length;
if (items === 0) {
    dv.paragraph("✅ Inbox zero");
} else {
    dv.paragraph(`📥 **${items}** items need processing`);
}
```

---

## 📊 This Week

### Due This Week
```dataview
TASK
FROM "Tracks"
WHERE !completed AND due >= date(today) AND due <= date(today) + dur(7 days)
SORT due ASC
```

### Waiting/Blocked
```dataview
TASK
FROM "Tracks"
WHERE !completed AND contains(text, "#waiting")
SORT file.name ASC
```

---

## 📁 Active Work

### Projects
```dataview
TABLE WITHOUT ID
  file.link as "Project",
  progress + "%" as "Progress",
  length(filter(file.tasks, (t) => !t.completed)) as "Tasks"
FROM "Tracks"
WHERE (contains(file.name, "p0") OR contains(file.name, "p1") OR contains(file.name, "p2"))
WHERE status = "active"
SORT file.mtime DESC
LIMIT 5
```

### Areas
```dataview
TABLE WITHOUT ID
  file.link as "Area",
  length(filter(file.tasks, (t) => !t.completed)) as "Tasks"
FROM "Tracks"
WHERE contains(file.name, "area-")
WHERE status = "active"
SORT file.name ASC
```

---

## 📈 Stats

```dataviewjs
const tracks = dv.pages('"Tracks"');
const projects = tracks.where(p => p.file.name.match(/^_p\d{3}/) && p.status === "active").length;
const areas = tracks.where(p => p.file.name.includes("area-") && p.status === "active").length;

const allTasks = tracks.file.tasks;
const open = allTasks.where(t => !t.completed).length;
const completed = allTasks.where(t => t.completed).length;
const overdue = allTasks.where(t => !t.completed && t.due && t.due < dv.date("today")).length;

dv.paragraph(`**Active:** ${projects} projects, ${areas} areas`);
dv.paragraph(`**Tasks:** ${open} open, ${completed} done`);
if (overdue > 0) {
    dv.paragraph(`**⚠️ Overdue:** ${overdue}`);
}
```

---

*Updated: `= date(today)`*
