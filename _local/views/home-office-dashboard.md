---
title: "Home Office Project Dashboard"
created: 2026-05-15
updated: 2026-06-26
tags:
  - dashboard
  - project
  - home-office
---

# 🏢 Home Office Project Dashboard

> **Quick Access:** [[_p001-home-office-home|Project Home]] | [[20260525-install-lighting|Critical Path]]

---

## 📊 Project Status

```dataview
TABLE WITHOUT ID
  title as "Project",
  status as "Status",
  progress + "%" as "Progress",
  summary as "Current State"
FROM "Tracks/p001-home-office"
WHERE file.name = "_p001-home-office-home"
```

---

## 💰 Budget Tracking

**Allocated:** $3,500
**Spent:** $2,240
**Committed:** $420 (lighting fixtures ordered)
**Remaining:** $840

### Breakdown by Category

| Category | Budgeted | Actual | Variance |
|----------|----------|--------|----------|
| Furniture | $2,000 | $1,840 | +$160 ✓ |
| Electrical | $400 | $280 | +$120 ✓ |
| Lighting | $450 | $280 (ordered) | +$170 ✓ |
| Accessories | $350 | $120 | +$230 ✓ |
| Contingency | $300 | $0 | +$300 ✓ |

**Status:** 🟢 Under budget across all categories

---

## 🎯 Critical Path Tasks

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  priority as "Priority",
  status as "Status",
  due_date as "Due",
  choice(status = "blocked", "⛔ BLOCKED", choice(status = "waiting", "⏸️ WAITING", "")) as "Alert"
FROM "Tracks/p001-home-office/tasks"
WHERE status != "complete"
SORT priority DESC, due_date ASC
```

---

## 📋 Task Breakdown

### By Status

```dataview
TABLE WITHOUT ID
  length(rows) as "Count"
FROM "Tracks/p001-home-office/tasks"
GROUP BY status
SORT length(rows) DESC
```

### By Priority

```dataview
TABLE WITHOUT ID
  length(rows) as "Count"
FROM "Tracks/p001-home-office/tasks"
WHERE status != "complete"
GROUP BY priority
SORT priority DESC
```

---

## ⏰ Timeline View

### This Week

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  status as "Status",
  due_date as "Due"
FROM "Tracks/p001-home-office/tasks"
WHERE due_date >= date(today) AND due_date <= date(today) + dur(7 days)
SORT due_date ASC
```

### Overdue

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  priority as "Priority",
  due_date as "Was Due",
  date(today) - due_date as "Days Overdue"
FROM "Tracks/p001-home-office/tasks"
WHERE due_date < date(today) AND status != "complete"
SORT due_date ASC
```

---

## 🔗 Key Resources

### People

```dataview
TABLE WITHOUT ID
  link(file.link, full_name) as "Name",
  role as "Role",
  phone as "Phone"
FROM "Tracks/p001-home-office/resources"
WHERE type = "contact"
```

### Vendors

```dataview
TABLE WITHOUT ID
  link(file.link, company_name) as "Company",
  service_type as "Service",
  contact_name as "Contact"
FROM "Tracks/p001-home-office/resources"
WHERE type = "vendor"
```

### Tools & Services

```dataview
TABLE WITHOUT ID
  link(file.link, name) as "Tool",
  purpose as "Purpose"
FROM "Tracks/p001-home-office/resources"
WHERE type = "tool"
```

---

## 🏆 Completed Milestones

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  choice(parent_task != null and parent_task != "", "↳ Child Task", "Parent Task") as "Type",
  created_date as "Started",
  file.mtime as "Completed"
FROM "Tracks/p001-home-office/zzz"
SORT file.mtime DESC
LIMIT 5
```

---

## 📝 Recent Activity

```dataview
TABLE WITHOUT ID
  file.mtime as "Date",
  link(file.link, title) as "File",
  choice(contains(file.folder, "tasks"), "Task", choice(contains(file.folder, "resources"), "Resource", "Home Doc")) as "Type"
FROM "Tracks/p001-home-office"
WHERE file.mtime >= date(today) - dur(14 days)
SORT file.mtime DESC
LIMIT 10
```

---

## 🚧 Blockers & Dependencies

### Current Blockers

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  priority as "Priority",
  "See task notes" as "Blocker Details"
FROM "Tracks/p001-home-office/tasks"
WHERE status = "blocked"
```

### Waiting On

```dataview
TABLE WITHOUT ID
  link(file.link, title) as "Task",
  "See task notes for dependency" as "Waiting For"
FROM "Tracks/p001-home-office/tasks"
WHERE status = "waiting"
```

---

## 📈 Progress Indicators

### Task Completion Rate

```dataviewjs
const allTasks = dv.pages('"Tracks/p001-home-office/tasks" OR "Tracks/p001-home-office/zzz"');
const completed = allTasks.where(t => t.status === "complete").length;
const total = allTasks.length;
const percentage = Math.round((completed / total) * 100);

dv.header(3, `${completed} of ${total} tasks complete (${percentage}%)`);

// Simple progress bar
const barWidth = 30;
const filled = Math.round((completed / total) * barWidth);
const empty = barWidth - filled;
const bar = "█".repeat(filled) + "░".repeat(empty);

dv.paragraph(`\`${bar}\` ${percentage}%`);
```

### Effort Distribution

```dataviewjs
const tasks = dv.pages('"Tracks/p001-home-office/tasks"');
const effort = {
  simple: tasks.where(t => t.effort === "simple").length,
  moderate: tasks.where(t => t.effort === "moderate").length,
  complex: tasks.where(t => t.effort === "complex").length
};

dv.table(
  ["Effort Level", "Count"],
  [
    ["Simple", effort.simple],
    ["Moderate", effort.moderate],
    ["Complex", effort.complex]
  ]
);
```

---

## 🎬 Next Actions

1. **Install lighting** (Dec 27) - Critical path, blocks monitor mounting
2. **Mount monitor arms** (Dec 27) - Dependent on lighting completion
3. **Cable management** (Jan 2+) - Waiting on tray delivery Dec 28
4. **Ergonomic setup** (Jan 5) - Final configuration after equipment installed

---

## 📌 Quick Notes

- Mike Torres scheduled for Dec 27 (lighting + monitor arms)
- Cable tray arriving Dec 28 - can proceed with management afterward
- Budget has healthy contingency remaining ($840)
- No cost overruns on any category so far
- Project on track for early January completion

---

*Dashboard auto-updates with Dataview queries. Last manual update: 2026-06-26*
