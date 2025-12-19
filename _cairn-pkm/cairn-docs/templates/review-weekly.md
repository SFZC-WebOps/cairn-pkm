# review-weekly Template

Create a structured weekly review document with Dataview queries.

---

## Prompts

None — automatically uses current week.

---

## Output

**Filename:** `review-YYYY-WNN.md`

---

## Generated File

The template creates a comprehensive weekly review with five phases:

### Phase 1: Clear (15 min)

- Dataview list of Capture/ inbox items
- External inbox checklist (email, browser tabs, physical, notes app)

### Phase 2: Review Projects (20 min)

- Dataview table of active projects with task counts
- Questions: Next action? Still active? Blockers?

### Phase 3: Review Areas (15 min)

- Dataview table of active areas
- Health check: Focus documented? Responsibilities on track?

### Phase 4: Review Tasks (10 min)

- Overdue tasks query
- Due this week query
- Waiting/blocked query
- Task cleanup checklist

### Phase 5: Plan Next Week (10 min)

- Top 3 priorities
- Key commitments by day
- Time blocks needed

### Reflection

- What worked well
- What didn't work
- What to do differently

### Checklist

Final verification that all phases complete.

---

## Dataview Queries Used

```dataview
TABLE FROM "Capture"
WHERE status = "unprocessed" OR !status
```

```dataview
TABLE FROM "Tracks"
WHERE type = "project" AND status = "active"
```

```dataview
TABLE FROM "Tracks"
WHERE type = "area" AND status = "active"
```

```dataview
TASK FROM "Tracks"
WHERE !completed AND due < date(today)
```

```dataview
TASK FROM "Tracks"
WHERE !completed AND due >= date(today) AND due <= date(today) + dur(7 days)
```

---

## Suggested Schedule

| Phase | Duration | Focus |
|-------|----------|-------|
| Clear | 15 min | Process inbox to zero |
| Projects | 20 min | Review each active project |
| Areas | 15 min | Check ongoing responsibilities |
| Tasks | 10 min | Address overdue and blocked |
| Plan | 10 min | Set up next week |

**Total:** ~70 minutes

---

*Back to [Templates Index](index.md)*
