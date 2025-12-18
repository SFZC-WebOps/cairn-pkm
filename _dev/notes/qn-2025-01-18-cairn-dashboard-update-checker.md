# Quick Note: CAIRN Dashboard with Update Checker

Created: 2025-01-18

## Context

Design for dashboard that shows update notifications when new CAIRN releases are available.

## Implementation

Create `_CAIRN/views/dashboard.md`:

````markdown
# CAIRN Dashboard

## System Status

```js-engine
const currentVersion = await dv.io.load("_CAIRN/VERSION");
const vMatch = currentVersion.match(/version:\s*(\S+)/);
const current = vMatch ? vMatch[1] : 'unknown';

try {
  const response = await fetch('https://api.github.com/repos/SFZC-WebOps/cairn-pkm/releases/latest');
  const data = await response.json();
  const latest = data.tag_name.replace('v', '');
  
  if (current === latest) {
    dv.paragraph(`✓ **Up to date** - v${current}`);
  } else {
    dv.paragraph(`📦 **Update available:** [v${latest}](${data.html_url}) (you have v${current})`);
  }
} catch (error) {
  dv.paragraph(`**Current version:** v${current} | [Check for updates](https://github.com/SFZC-WebOps/cairn-pkm/releases/latest)`);
}
```

---

## Your Work

### Due This Week
```dataview
TABLE WITHOUT ID
  file.link as "Task",
  project as "Track",
  due_date as "Due",
  priority as "Priority"
FROM "Tracks"
WHERE contains(file.folder, "tasks")
  AND status != "complete"
  AND due_date >= date(today)
  AND due_date <= date(today) + dur(7 days)
SORT due_date ASC
LIMIT 10
```

### Overdue
```dataview
TABLE WITHOUT ID
  file.link as "Task",
  project as "Track",
  due_date as "Due Date"
FROM "Tracks"
WHERE contains(file.folder, "tasks")
  AND status != "complete"
  AND due_date < date(today)
SORT due_date ASC
```

### Active Projects
```dataview
TABLE WITHOUT ID
  file.link as "Project",
  status as "Status",
  progress + "%" as "Progress"
FROM "Tracks"
WHERE type = "project"
  AND status = "active"
SORT file.name ASC
```
````

## Setup Requirements

Add to SETUP.md:

```markdown
### Dataview Configuration
Settings → Dataview:
- ✓ Enable JavaScript Queries
- ✓ Enable Inline JavaScript Queries

Required for update checker on dashboard.
```

## User Experience

**Up to date:**
```
✓ Up to date - v1.1.0
```

**Update available:**
```
📦 Update available: v1.2.0 (you have v1.1.0)
```
(Clickable link to release page)

**Offline fallback:**
```
Current version: v1.1.0 | Check for updates
```

## Notes

- Checks GitHub API for latest release
- Compares with `_CAIRN/VERSION` file
- No authentication needed (public repo)
- Graceful fallback if offline
- Users click link → download → replace `_CAIRN/`

---

Saved for implementation when setting up CAIRN-dev vault.
