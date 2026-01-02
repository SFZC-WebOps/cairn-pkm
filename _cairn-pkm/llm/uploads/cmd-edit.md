# Editing Things
*Type: Read/Write | Updated: 2025-01-01*

## Overview

Edit areas, projects, tasks, and objects through natural conversation. Tell the system what you want to change — it figures out the file, shows the change, confirms before saving.

**Shortcut:** `!edit [filename]` for direct access

---

## Conversational Editing

### How It Works

```
User: Mark the SSL task complete

LLM: [Searches for SSL-related tasks]

     Found: 20250101-ssl-cert-update.md in p014-site-rdsg
     
     I'll update it:
     - status: active → complete
     - Add history: "2025-01-01: Marked complete"
     
     Save this?

User: Yes, and add a note that we set up auto-renewal

LLM: Updated:
     - status: active → complete
     - Add history: "2025-01-01: Marked complete — auto-renewal configured"
     
     Save this?

User: Yes

LLM: ✓ Done — SSL task marked complete.
```

### Finding the Right File

The system searches based on what you say:

| User Says | System Searches |
|-----------|-----------------|
| "the SSL task" | Tasks containing "SSL" |
| "project home" | Current project's _*-home.md |
| "my finance area" | area-finance home doc |
| "the Acme contact" | Objects with "Acme" in name |
| "20250101-ssl-cert" | Direct filename match |

If multiple matches: "I found a few things that could match — which one?"

If no match: "I couldn't find that. What's it called?"

---

## Editing Tasks

### What You Can Change

Just say what you want:

```
"Mark it complete"
"Set priority to critical"  
"Push the due date to next Friday"
"Add a subtask for testing"
"Add a note that we're waiting on vendor response"
"Change status to blocked"
```

### Confirmation Format

```
LLM: I'll update 20250101-ssl-cert-update.md:

     Changes:
     - status: active → complete
     - Add history: "2025-01-01: Marked complete — cert renewed successfully"
     
     Save this?
```

### What Gets Updated

- Frontmatter fields (status, priority, due_date, etc.)
- Task History section (prepended)
- Subtasks section (appended)
- Notes section (appended)

### History Entry Format (RC Principle)

Always capture *why*, not just *what*:

```
Good: "2025-01-01: Marked complete — auto-renewal now configured"
Good: "2025-01-01: Set to blocked — waiting on vendor credentials"
Bad:  "2025-01-01: Marked complete"
```

If user doesn't provide rationale, ask:
```
LLM: Why is this being marked complete? (helps future you understand)
```

---

## Editing Projects

### What You Can Change

```
"Update progress to 75%"
"Set status to on hold"
"Add a log entry — Sprint 2 complete"
"Update the summary to 'Waiting on final approval'"
"Change the end date to March"
```

### Confirmation Format

```
LLM: I'll update _p014-site-rdsg-home.md:

     Changes:
     - progress: 50 → 75
     - modified: 2024-12-15 → 2025-01-01
     - Add log: "2025-01-01 - Milestone - Sprint 2 complete"
     
     Save this?
```

### What Gets Updated

- Frontmatter: status, progress, summary, dates, modified
- Log section (appended with timestamp)

### Log Entry Format

```
{date} {time} - {Type} - {Description}
```

Types: decision, update, milestone, issue, note

Include context (RC principle):
```
Good: "2025-01-01 14:30 - Decision - Deferring launch to Feb — waiting on legal review"
Bad:  "2025-01-01 14:30 - Decision - Deferring launch"
```

---

## Editing Areas

Same as projects:

```
"Set area to on hold"
"Add a log entry — switching to quarterly reviews"
"Update the description"
```

---

## Editing Objects

### What You Can Change

```
"Update Jennifer's email to jennifer@newdomain.com"
"Mark the old account as inactive"
"Add a note that contract renews in March"
```

### Confirmation Format

```
LLM: I'll update contact-jennifer-martinez.md:

     Changes:
     - email: jennifer@acme.com → jennifer@newdomain.com
     - lastmod: 2024-11-15 → 2025-01-01
     
     Save this?
```

### What Gets Updated

- Frontmatter fields
- lastmod (always updated on any edit)
- Notes section (appended)

---

## Shortcut: !edit

For direct access:

```
!edit 20250101-ssl-cert-update.md
!edit _p014-site-rdsg-home.md
!edit contact-jennifer-martinez.md
```

Behavior:
1. Find the file
2. Show current state
3. Ask "What do you want to change?"
4. Confirm and save

---

## File Resolution

When searching for a file:

1. Check Tracks/*/tasks/ (task files)
2. Check Tracks/*/_*-home.md (project/area homes)
3. Check Objects/ (objects)
4. Check Tracks/*/resources/ (track-specific objects)

### Type Detection

Determined by:
- Frontmatter `type` field
- Filename pattern (_area-*, _p###-*, YYYYMMDD-*, contact-*, etc.)
- File location

---

## Auto-Updated Fields

On ANY edit, these update automatically:

| Entity | Field | Notes |
|--------|-------|-------|
| Area | modified | Always updated |
| Project | modified | Always updated |
| Object | lastmod | Always updated |
| Task | (none) | Task History is the audit trail |

---

## Output Behavior

After confirmation:

1. Check prefs for file_operations setting
2. If "write" and filesystem available → Write directly
3. If "download" or no filesystem → Show content, provide download
4. Report success

```
LLM: ✓ Done — task marked complete.
```

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| File not found | "I couldn't find '[name]'. Did you mean one of these? [list similar]" |
| Multiple matches | "There are a few things called [x]. Which one? [list]" |
| Can't parse file | "That file has a formatting issue. Want me to try to fix it?" |
| Invalid value | "That's not a valid status. Options are: [list]" |

---

## Field Reference

See `cmd-shared-patterns.md` Field Enums for valid values:

**Tasks:** status, priority, phase, effort, viz
**Projects:** status (planning, active, onhold, complete, archived)
**Areas:** status (active, onhold, archived)
**Objects:** status (active, inactive, archived)
