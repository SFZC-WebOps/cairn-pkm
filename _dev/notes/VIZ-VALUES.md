# Task Viz Field Values

The `viz` field helps surface tasks in dashboard views based on when and how you need to see them.

## Standard Values

| Value | Use Case | When to Use |
|-------|----------|-------------|
| `today` | Must see/do today | Tasks requiring attention today, urgent items |
| `this-week` | This week's priorities | Tasks due this week, weekly goals |
| `upcoming` | Next 2-4 weeks | Planning ahead, tasks coming due soon |
| `waiting` | Blocked/delegated | Waiting on others, dependencies, follow-ups |
| `routine` | Daily/weekly recurring | Regular check-ins, ongoing monitoring |
| `review` | Needs decision/approval | Items awaiting review, approvals needed |
| `backlog` | When time permits | Low priority, nice-to-have, someday/maybe |

## Dashboard Views

These values map to dashboard views:

**Today's Focus** (`viz: today`)
- High priority items
- Time-sensitive tasks
- Quick wins for the day

**Week Ahead** (`viz: this-week`)
- Weekly priorities
- Coordinated activities
- Deadlines this week

**Coming Soon** (`viz: upcoming`)
- Planning horizon
- Prepare for future work
- Early awareness

**Waiting On** (`viz: waiting`)
- Delegated tasks
- External dependencies
- Follow-up needed

**Routine Checks** (`viz: routine`)
- Daily/weekly recurring
- Monitoring tasks
- Regular maintenance

**Needs Review** (`viz: review`)
- Decisions required
- Approval requests
- Quality checks

**Backlog** (`viz: backlog`)
- Low priority
- Nice-to-have
- Future consideration

## Usage Notes

- One viz value per task
- Choose based on **when you need to see it**, not importance
- Priority field handles importance (low/medium/high/critical)
- Status field handles workflow (active/blocked/waiting/etc.)
- Viz field handles **dashboard surfacing**

## Examples

```yaml
# Must review expense reports today
viz: today

# Weekly team meeting prep
viz: this-week

# Project starting next month
viz: upcoming

# Waiting on vendor quote
viz: waiting

# Check email daily
viz: routine

# Draft needs approval
viz: review

# Idea for future improvement
viz: backlog
```
