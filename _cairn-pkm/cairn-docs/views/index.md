# Views

Dataview dashboards for visualizing vault content.

---

## Overview

Views use [Dataview](https://github.com/blacksmithgu/obsidian-dataview) to query and display information across the vault.

**Location:** `_cairn-pkm/views/`

---

## Available Views

| View | Purpose |
|------|---------|
| [tasks-dashboard](tasks-dashboard.md) | Main task view with filters |
| [tracks-viewer](tracks-viewer.md) | Project/area browser |
| [tasks-overdue](tasks-overdue.md) | Overdue task alerts |
| [tracks-projects](tracks-projects.md) | Active project overview |
| [capture-inbox](capture-inbox.md) | Inbox status |
| [review-weekly](review-weekly.md) | Weekly dashboard |

---

## Quick Reference

### tasks-dashboard

Main task management view.

**Features:**
- Group by project and viz tag
- Collapsible sections
- Parent-child task relationships
- Subtask display
- Overdue indicators
- Due date countdown

**Toggle:** Checkbox to show all viz tags or just 11/eyeson

---

### tracks-viewer

Browse all projects and areas.

**Features:**
- Dropdown selector
- Task list by section
- Project summaries
- Status indicators

---

### tasks-overdue

Focus on time-sensitive items.

**Sections:**
- Overdue tasks
- Due today
- Due this week
- Summary counts

---

### tracks-projects

Project health overview.

**Shows:**
- Active projects with progress
- Task counts (open/done)
- Health indicators
- Recently updated
- Stale projects (14+ days)

---

### capture-inbox

Inbox processing status.

**Shows:**
- Unprocessed items
- Days old
- Inbox count
- Items older than 7 days

---

### review-weekly

Combined weekly dashboard.

**Sections:**
- Attention needed (overdue, inbox)
- This week (due, waiting)
- Active work (projects, areas)
- Stats summary

---

## Custom Views

Add vault-specific views to `_local/views/`.

They won't be affected by Cairn-PKM updates.

---

## Dataview Configuration

Required settings:
- ✅ Enable JavaScript Queries
- ✅ Enable Inline JavaScript Queries

---

*Back to [README](../README.md)*
