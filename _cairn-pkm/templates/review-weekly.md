<%*
const weekNum = tp.date.now("YYYY-[W]ww");
const today = tp.date.now("YYYY-MM-DD");
const fileName = `review-${weekNum}`;
await tp.file.rename(fileName);
-%>
---
title: "Weekly Review - <% tp.date.now("YYYY-[W]ww") %>"
date: <% tp.date.now("YYYY-MM-DD") %>
type: review
status: active
tags:
  - review
  - weekly
---

# Weekly Review — <% tp.date.now("YYYY-[W]ww") %>

**Date:** <% tp.date.now("dddd, MMMM D, YYYY") %>

---

## Phase 1: Clear (15 min)

### Capture/ Inbox
```dataview
TABLE WITHOUT ID
  file.link as "Item",
  file.ctime as "Captured"
FROM "Capture"
SORT file.ctime DESC
```

**Processing plan:**
- [ ] Process each item → Track, Objects, or delete
- [ ] Inbox zero by end of review

### External inboxes
- [ ] Email inbox processed
- [ ] Browser tabs closed or captured
- [ ] Physical inbox cleared
- [ ] Notes app emptied

---

## Phase 2: Review Projects (20 min)

### Active Projects
```dataview
TABLE WITHOUT ID
  file.link as "Project",
  status as "Status",
  progress as "Progress",
  length(filter(file.tasks, (t) => !t.completed)) as "Open Tasks"
FROM "Tracks"
WHERE type = "project" AND status = "active"
SORT file.mtime DESC
```

**For each project, ask:**
- What's the next action?
- Is it still active, or should status change?
- Any blockers to flag?

**Project notes:**
- 

---

## Phase 3: Review Areas (15 min)

### Active Areas
```dataview
TABLE WITHOUT ID
  file.link as "Area",
  length(filter(file.tasks, (t) => !t.completed)) as "Open Tasks"
FROM "Tracks"
WHERE type = "area" AND status = "active"
SORT file.name ASC
```

**Area health check:**
- [ ] Each area has current focus documented
- [ ] Recurring responsibilities on track
- [ ] No neglected areas

**Area notes:**
- 

---

## Phase 4: Review Tasks (10 min)

### Overdue Tasks
```dataview
TASK
FROM "Tracks"
WHERE !completed AND due < date(today)
SORT due ASC
```

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
```

**Task cleanup:**
- [ ] Reschedule or complete overdue items
- [ ] Follow up on waiting items
- [ ] Ensure next actions are clear

---

## Phase 5: Plan Next Week (10 min)

### Top 3 Priorities
1. 
2. 
3. 

### Key Commitments
| Day | Commitment |
|-----|------------|
| Mon | |
| Tue | |
| Wed | |
| Thu | |
| Fri | |

### Time Blocks Needed
- [ ] Deep work block: 
- [ ] Admin block: 
- [ ] Review/planning: 

---

## Reflection

### What worked well this week?
- 

### What didn't work?
- 

### What will I do differently?
- 

---

## Review Checklist

- [ ] Capture/ at zero
- [ ] All projects reviewed
- [ ] All areas checked
- [ ] Overdue tasks addressed
- [ ] Next week planned
- [ ] Calendar reviewed

---

*Review completed: <% tp.date.now("YYYY-MM-DD HH:mm") %>*
