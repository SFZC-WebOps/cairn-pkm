# review-weekly View

Combined dashboard for weekly reviews.

---

## Location

`_cairn-pkm/views/review-weekly.md`

---

## Sections

### 🚨 Attention Needed

**Overdue Tasks:**
```dataview
TASK FROM "Tracks"
WHERE !completed AND due AND due < date(today)
LIMIT 10
```

**Inbox Items:**
Count of unprocessed captures.

### 📊 This Week

**Due This Week:**
Tasks due in next 7 days.

**Waiting/Blocked:**
Tasks with #waiting tag.

### 📁 Active Work

**Projects:**
Top 5 active projects with progress and task counts.

**Areas:**
All active areas with task counts.

### 📈 Stats

Summary of:
- Active projects count
- Active areas count
- Open task count
- Completed task count
- Overdue count (if any)

---

## Use Cases

- Weekly review starting point
- Daily check-in dashboard
- Quick status overview

---

## Related

- [review-weekly template](../templates/review-weekly.md) — Creates dated review document
- [tasks-overdue](tasks-overdue.md) — Detailed overdue view
- [capture-inbox](capture-inbox.md) — Detailed inbox view

---

*Back to [Views Index](index.md)*
