---
title: "Weekly Review Dashboard"
created: 2025-11-05
updated: 2025-12-22
tags:
  - dashboard
  - review
  - weekly
---

# 📅 Weekly Review Dashboard

> **Purpose:** Weekly reflection and planning checkpoint

**Review Date:** `= date(today)` | **Week:** `= dateformat(date(today), "yyyy-'W'WW")`

---

## ✅ Review Checklist

- [ ] Review inbox (Capture/) and process all items
- [ ] Check for overdue tasks and reschedule/complete
- [ ] Review upcoming week's due dates
- [ ] Unblock any blocked tasks where possible
- [ ] Update project progress percentages
- [ ] Archive completed tasks to zzz/ folders
- [ ] Review budget status for active projects
- [ ] Plan 2-3 key priorities for next week

---

## 📥 Inbox Processing

### Unprocessed Captures

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Item",
  capture_type as "Type",
  captured as "Captured",
  date(today) - captured as "Age (days)"
FROM "Capture"
WHERE processed = false
SORT captured ASC
```

**Action:** Process each item - convert to task, save as reference, or delete.

---

## 🔴 Attention Required

### Overdue Items

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

### Critically Overdue (90+ Days)

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  due_date as "Original Due",
  "⚠️ Review needed - still relevant?" as "Action"
FROM "Tracks"
WHERE due_date < date(today) - dur(90 days) AND status != "complete"
SORT due_date ASC
```

**Action:** For each item - complete, reschedule with new due date, or mark as deferred/cancelled.

---

## 📅 Week Ahead

### Due This Week

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  due_date as "Due"
FROM "Tracks"
WHERE due_date >= date(today) AND due_date <= date(today) + dur(7 days) AND status != "complete"
SORT due_date ASC
```

### Due Next Week

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  due_date as "Due"
FROM "Tracks"
WHERE due_date > date(today) + dur(7 days) AND due_date <= date(today) + dur(14 days) AND status != "complete"
SORT due_date ASC
```

**Action:** Ensure upcoming tasks have clear next actions and aren't blocked.

---

## 🚧 Blocked Items Review

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  created_date as "Created",
  "Review blocker status" as "Action"
FROM "Tracks"
WHERE status = "blocked"
SORT priority DESC, created_date ASC
```

**Questions for each:**
- Is the blocker still valid?
- Can I work around it?
- Can I follow up with someone to unblock?
- Should this be deferred instead of blocked?

---

## ⏸️ Waiting Items Review

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  priority as "Priority",
  created_date as "Created"
FROM "Tracks"
WHERE status = "waiting"
SORT priority DESC, created_date ASC
```

**Questions for each:**
- Have I heard back yet?
- Should I follow up?
- Is there related work I can do while waiting?

---

## 📈 Project Health Check

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Project",
  status as "Status",
  progress + "%" as "Progress",
  summary as "Summary",
  choice(progress >= 75, "🟢 On Track", choice(progress >= 40, "🟡 Monitor", "🔴 Needs Attention")) as "Health"
FROM "Tracks"
WHERE type = "project"
SORT progress ASC
```

**Action:** For each project, consider updating progress % and summary if work has been done this week.

---

## 🎯 Accomplishments This Week

### Tasks Completed (Last 7 Days)

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  file.mtime as "Completed"
FROM "Tracks"
WHERE status = "complete" AND file.mtime >= date(today) - dur(7 days)
SORT file.mtime DESC
```

**Reflection:** What went well? What enabled these completions?

---

## 📊 Effort Distribution

### Active Tasks by Effort Level

```dataviewjs
const tasks = dv.pages('"Tracks"')
  .where(p => p.file.folder.includes("/tasks") && p.status !== "complete");

const effort = {
  simple: tasks.where(t => t.effort === "simple").length,
  moderate: tasks.where(t => t.effort === "moderate").length,
  complex: tasks.where(t => t.effort === "complex").length
};

const total = effort.simple + effort.moderate + effort.complex;

dv.table(
  ["Effort", "Count", "Percentage"],
  [
    ["Simple", effort.simple, total > 0 ? Math.round((effort.simple/total)*100) + "%" : "0%"],
    ["Moderate", effort.moderate, total > 0 ? Math.round((effort.moderate/total)*100) + "%" : "0%"],
    ["Complex", effort.complex, total > 0 ? Math.round((effort.complex/total)*100) + "%" : "0%"]
  ]
);

dv.paragraph(`**Total active tasks:** ${total}`);
```

**Balance check:** Too many complex tasks? Need some quick wins (simple tasks)?

---

## 🗂️ Cleanup Actions

### Tasks to Archive

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  project as "Track",
  file.mtime as "Completed"
FROM "Tracks"
WHERE status = "complete" AND file.folder != null AND !contains(file.folder, "zzz")
SORT file.mtime ASC
LIMIT 10
```

**Action:** Move completed tasks from `/tasks` to `/zzz` folders for archival.

### Old Captures to Process

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Item",
  capture_type as "Type",
  captured as "Date",
  date(today) - captured as "Age (days)"
FROM "Capture"
WHERE processed = false AND date(today) - captured > dur(14 days)
SORT captured ASC
```

**Action:** Process or delete old captures - if not processed in 2+ weeks, likely not important.

---

## 🎯 Next Week Priorities

**Top 3 Priorities for Next Week:**

1. _______________________________________________
2. _______________________________________________
3. _______________________________________________

**Key Focus Areas:**
- _______________________________________________ (Track/Project)
- _______________________________________________ (Track/Project)

**Time Allocation:**
- Simple tasks (quick wins): ______ hours
- Moderate tasks: ______ hours  
- Complex tasks (deep work): ______ hours

---

## 💡 Reflections & Notes

### What went well this week?


### What could be improved?


### Key learnings or insights?


### Blockers or challenges?


---

## 📋 Review Summary

```dataviewjs
const captures = dv.pages('"Capture"').where(p => p.processed === false).length;
const overdue = dv.pages('"Tracks"')
  .where(p => p.file.folder && p.file.folder.includes("/tasks") && 
              p.status !== "complete" && p.due_date && p.due_date < dv.date("today"))
  .length;
const blocked = dv.pages('"Tracks"')
  .where(p => p.status === "blocked")
  .length;
const dueThisWeek = dv.pages('"Tracks"')
  .where(p => p.file.folder && p.file.folder.includes("/tasks") && 
              p.status !== "complete" && p.due_date && 
              p.due_date >= dv.date("today") && 
              p.due_date <= dv.date("today") + dv.duration("7 days"))
  .length;

dv.header(3, "Items Requiring Attention:");
dv.list([
  `📥 **${captures}** unprocessed captures`,
  `🔴 **${overdue}** overdue tasks`,
  `🚧 **${blocked}** blocked tasks`,
  `📅 **${dueThisWeek}** tasks due this week`
]);

const totalAction = captures + overdue + blocked;
if (totalAction === 0) {
  dv.paragraph("✅ **Excellent!** Everything is current. Good week for planning ahead.");
} else if (totalAction <= 5) {
  dv.paragraph("🟢 **Good shape.** A few items to address, but manageable.");
} else if (totalAction <= 10) {
  dv.paragraph("🟡 **Moderate cleanup needed.** Set aside time this week to process backlog.");
} else {
  dv.paragraph("🔴 **Heavy cleanup needed.** Consider dedicating a focused session to clearing backlog.");
}
```

---

*Weekly review dashboard - bookmark this page and visit every Sunday/Monday*
