---
title: "All Tracks Overview"
created: 2026-05-10
updated: 2026-06-26
tags:
  - dashboard
  - overview
  - tracks
---

# 🗺️ All Tracks Overview

> **Quick Navigation:** [[area-personal/_area-personal-home|Personal]] | [[p001-home-office/_p001-home-office-home|Home Office]]

---

## 📊 Track Status Summary

### Areas

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Area",
  status as "Status",
  choice(file.mtime >= date(today) - dur(7 days), "🟢 Recent", choice(file.mtime >= date(today) - dur(30 days), "🟡 Active", "🔵 Stable")) as "Activity"
FROM "Tracks"
WHERE type = "area" AND contains(file.name, "-home")
SORT file.mtime DESC
```

### Projects

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Project",
  status as "Status",
  progress + "%" as "Progress",
  choice(progress >= 75, "🟢", choice(progress >= 40, "🟡", "🔴")) as "Health"
FROM "Tracks"
WHERE type = "project" AND contains(file.name, "-home")
SORT progress DESC
```

---

## 🎯 High Priority Tasks (All Tracks)

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  status as "Status",
  due_date as "Due",
  choice(due_date < date(today), "🔴 OVERDUE", choice(due_date <= date(today) + dur(7 days), "🟡 Soon", "")) as "Alert"
FROM "Tracks"
WHERE status != "complete" AND (priority = "high" OR priority = "critical")
SORT priority DESC, due_date ASC
```

---

## ⏰ Time-Sensitive Tasks

### Due This Week

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  due_date as "Due Date"
FROM "Tracks"
WHERE due_date >= date(today) AND due_date <= date(today) + dur(7 days) AND status != "complete"
SORT due_date ASC
```

### Overdue Tasks

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  due_date as "Was Due",
  date(today) - due_date as "Days Over"
FROM "Tracks"
WHERE due_date < date(today) AND status != "complete"
SORT due_date ASC
```

---

## 🚦 Tasks by Status

### Active Tasks

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  phase as "Phase"
FROM "Tracks"
WHERE status = "active"
SORT priority DESC, project ASC
```

### Blocked Tasks

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  "See task for blocker details" as "Note"
FROM "Tracks"
WHERE status = "blocked"
```

### Waiting Tasks

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  "See task for dependency" as "Note"
FROM "Tracks"
WHERE status = "waiting"
```

---

## 📈 Activity Metrics

### Task Counts by Track

```dataviewjs
const tracks = {};

// Get all active tasks grouped by track
const tasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes("/tasks") && p.status !== "complete");

tasks.forEach(task => {
  const track = task.project || "Unassigned";
  tracks[track] = (tracks[track] || 0) + 1;
});

// Convert to array and sort
const sortedTracks = Object.entries(tracks)
  .map(([track, count]) => [track, count])
  .sort((a, b) => b[1] - a[1]);

if (sortedTracks.length > 0) {
  dv.table(["Track", "Active Tasks"], sortedTracks);
} else {
  dv.paragraph("No active tasks found.");
}
```

### Tasks Completed (Last 30 Days)

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  file.mtime as "Completed"
FROM "Tracks"
WHERE status = "complete" AND file.mtime >= date(today) - dur(30 days)
SORT file.mtime DESC
LIMIT 10
```

---

## 🎨 Visualization Tags

### Now / Next / Soon

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  viz as "Viz"
FROM "Tracks"
WHERE viz = "now" OR viz = "next" OR viz = "soon"
SORT 
  choice(viz = "now", 1, choice(viz = "next", 2, 3)),
  priority DESC
```

---

## 📝 Recent Captures

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Capture",
  capture_type as "Type",
  captured as "Date",
  choice(processed = false, "🔴 Unprocessed", "✅ Processed") as "Status"
FROM "Capture"
SORT captured DESC
LIMIT 10
```

---

## 🔗 Key Contacts (Cross-Track)

```dataview
TABLE WITHOUT ID
  link(file.link, full_name) as "Name",
  role as "Role",
  choice(email != null and email != "", "✉️", "") as "Email",
  choice(phone != null and phone != "", "📞", "") as "Phone"
FROM "Objects" OR "Tracks"
WHERE type = "contact"
SORT full_name ASC
```

---

## 📊 Overall Progress

### Completion Rate (Last 30 Days)

```dataviewjs
const allTasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes("/tasks") || p.file.folder.includes("/zzz"));

const recentlyCompleted = allTasks
  .where(t => t.status === "complete" && t.file.mtime >= dv.date("today") - dv.duration("30 days"))
  .length;

const stillActive = allTasks
  .where(t => t.status !== "complete")
  .length;

const completionRate = recentlyCompleted > 0 
  ? Math.round((recentlyCompleted / (recentlyCompleted + stillActive)) * 100)
  : 0;

dv.header(3, `${recentlyCompleted} tasks completed in last 30 days`);
dv.paragraph(`**Completion rate:** ${completionRate}% (${recentlyCompleted} completed vs ${stillActive} still active)`);
```

### Effort Distribution (Active Tasks)

```dataviewjs
const activeTasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes("/tasks") && p.status !== "complete");

const effort = {
  simple: activeTasks.where(t => t.effort === "simple").length,
  moderate: activeTasks.where(t => t.effort === "moderate").length,
  complex: activeTasks.where(t => t.effort === "complex").length,
  unset: activeTasks.where(t => !t.effort || t.effort === "").length
};

dv.table(
  ["Effort Level", "Tasks"],
  [
    ["Simple", effort.simple],
    ["Moderate", effort.moderate],
    ["Complex", effort.complex],
    ["Not Set", effort.unset]
  ]
);
```

---

## 🎯 Focus Recommendations

```dataviewjs
const now = dv.date("today");

// Get critical/high priority tasks
const urgent = dv.pages('"Tracks"')
  .where(p => 
    p.file.folder.includes("/tasks") && 
    p.status !== "complete" && 
    (p.priority === "critical" || p.priority === "high")
  );

// Get overdue tasks
const overdue = dv.pages('"Tracks"')
  .where(p => 
    p.file.folder.includes("/tasks") && 
    p.status !== "complete" && 
    p.due_date && 
    p.due_date < now
  );

// Get blocked tasks
const blocked = dv.pages('"Tracks"')
  .where(p => 
    p.file.folder.includes("/tasks") && 
    p.status === "blocked"
  );

dv.header(3, "Recommended Actions:");
dv.list([
  `**${urgent.length}** high/critical priority tasks need attention`,
  `**${overdue.length}** overdue tasks require immediate action`,
  `**${blocked.length}** blocked tasks should be reviewed for unblocking`
]);

if (urgent.length === 0 && overdue.length === 0 && blocked.length === 0) {
  dv.paragraph("✅ No urgent items - good time for planning or working ahead!");
}
```

---

*Dashboard updates automatically with Dataview queries. Last manual update: 2026-06-26*
