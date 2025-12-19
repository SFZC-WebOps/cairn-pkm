# LLM Commands

Command specifications for AI-assisted workflows in Cairn-PKM.

---

## Overview

Commands are prefixed with `!` and execute specific workflows. They're designed to work with LLM assistants like Claude.

| Command | Purpose | Type | Permission |
|---------|---------|------|------------|
| [!hi](cmd-hi.md) | Work overview for a track | Read | None |
| [!bye](cmd-bye.md) | Session closure and logging | Display | None |
| [!task](cmd-task.md) | Task creation and editing | Display | None |
| [!qn](cmd-qn.md) | Quick note capture | Write | Confirmation |
| [!change](cmd-change.md) | Change tracking | Write | Confirmation |
| [!obj](cmd-obj.md) | Object management | Display/Write | Varies |
| [!readme](cmd-readme.md) | Reference research | Read/Write | None |
| [!sk](cmd-sk.md) | Skill evidence scraping | Display | None |

---

## Command Types

### Read Commands
Read files and display information. No modifications.
- `!hi` — Overview of a project/area

### Display Commands
Analyze and display. User copies output manually.
- `!bye` — Session summary for logging
- `!task` — Generate task file content
- `!sk` — Extract skill evidence

### Write Commands
Create or modify files. Require confirmation.
- `!qn` — Create quick note
- `!change` — Create changelog entry

---

## Quick Reference

### !hi — Work Overview

```
!hi-p14        # Project p14-xxxx-yyyy
!hi-area-finance   # Area overview
```

Shows: status, recent log, active tasks, overdue items.

**Output:** Comprehensive track report with task summaries.

---

### !bye — Session Closure

```
!bye
```

Generates from current conversation:
- Task history entry (one line)
- Log entry (timestamped)

**Output:** Ready-to-paste content for track home doc.

---

### !task — Task Management

```
!task-c        # Create new task
!task-e        # Edit existing task (upload first)
```

Interactive workflow:
1. Analyzes conversation
2. Generates draft
3. Prompts for required fields
4. `done` → displays complete file

**Output:** Complete task file with frontmatter.

---

### !qn — Quick Note

```
!qn            # Capture from conversation
!qn [topic]    # Capture specific topic
```

Extracts:
- Main topic
- Key findings
- Action items

**Output:** Creates `qn-YYYYMMDD-HHMMSS-slug.md` in `Capture/`.

---

### !change — Change Tracking

```
!change
```

Extracts from conversation:
- Systems affected
- Technologies used
- Problem/solution
- AI contribution

**Output:** Creates YAML changelog entry in `_local/data/changelog/`.

---

### !obj — Object Management

```
!obj           # Show usage help
!obj-c {type}  # Create object (contact, vendor, tool, etc.)
!obj-e         # Edit uploaded object
```

**Output:** Complete object file with type-specific fields.

---

### !readme — Reference Research

```
!readme        # Analyze URL in conversation
!readme [url]  # Analyze specific URL
```

Performs:
- Content extraction
- Author credibility research
- Strategic fit analysis
- Usefulness scoring (1-10)

**Output:** Saves to references folder with score in filename.

---

### !sk — Skill Evidence

```
!sk            # Analyze current conversation
!sk-report     # Generate portfolio snapshot
```

Identifies SFIA framework skills from conversation:
- Keywords and actions
- Level determination
- Confidence scoring

**Output:** Evidence entries for skill-evidence.md.

---

## Common Patterns

### Context Setting

`!hi-[target]` sets context for `!bye`:
```
!hi-p14        # Sets context to p14
... work session ...
!bye           # Uses p14 context for log entry
```

### Task Workflow

```
... discuss work needed ...
!task-c        # Create from conversation
priority high
status active
done           # Get complete file
```

### Change Documentation

```
... fix SSL issue ...
!change        # Extract change details
requested_by Dan
save           # Creates changelog entry
```

---

## File Locations

| Command | Reads From | Writes To |
|---------|------------|-----------|
| !hi | `Tracks/*/` | — |
| !bye | Conversation | Display only |
| !task | `Tracks/*/tasks/` | Display only |
| !qn | Conversation | `Capture/` |
| !change | Conversation | `_local/data/changelog/` |
| !obj | `Objects/`, `Tracks/*/resources/` | Display only |
| !readme | Web | References folder |
| !sk | Conversation | Display only |

---

*See individual command pages for detailed specifications.*
