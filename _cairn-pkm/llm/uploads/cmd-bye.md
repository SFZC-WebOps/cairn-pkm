# Session Closure
*Type: Display/Write | Updated: 2025-01-01*

## Overview

When work wraps up, the system generates a session summary with log entries. This happens proactively when work seems done, or on demand via `!bye`.

**Shortcut:** `!bye` to close session immediately

---

## Proactive Wrap-Up

When the conversation seems to be winding down:

```
LLM: Looks like we've covered what you needed. 
     
     You created 2 tasks and updated the project status.
     
     Want me to wrap up and generate a session log? 
     (This captures what we did for the project record)
```

### Triggers for Wrap-Up Offer

- User says "I think we're done" / "that's all" / "thanks"
- Long pause after completing work
- User asks "what did we do?" / "can you summarize?"
- Natural end of a work sequence

---

## What Gets Generated

### Task History Entry

A small snippet for individual task files:

```
Suggested task history entry:
───────────────────────────────────────────────
2025-01-01: Marked complete — auto-renewal configured

Add to: 20250101-ssl-cert-update.md (Task History section)
```

This captures what happened to *that specific task*.

### Project Log Entry

A broader session summary for the project home:

```
Suggested log entry:
───────────────────────────────────────────────
2025-01-01 14:30 - Update - Security hardening session

Created SSL renewal task and backup configuration.
Set up auto-renewal to prevent future expiration issues.

- Files: Created 2 tasks
- AI contribution: Generated renewal script

Add to: _p014-site-rdsg-home.md (Log section)
```

This captures what happened in *the session as a whole*.

### No Separate Session Log File

The useful bits go where they belong:
- Task-specific updates → task files
- Session summary → project home log

The conversation itself serves as the ephemeral record.

---

## Project Status Check

For projects, offer to update status/progress:

```
LLM: Before wrapping up — project status check:
     
     p014-site-rdsg is currently:
     - Status: active
     - Progress: 50%
     - Summary: "Sprint 2 complete"
     
     Want to update any of these? (or "looks good")

User: Progress is 60 now

LLM: Updated:
     - progress: 50 → 60
     
     [Continues with session log generation]
```

---

## Session Log Output

### Output Flow

**1. Display what was generated:**
```
LLM: Here's a summary of what we did:
     
     ─────────────────────────────────────────
     Primary track: p014-site-rdsg
     
     Created 2 tasks, updated project status.
     ─────────────────────────────────────────
     
     Task history entry (for 20250101-ssl-cert-update.md):
     > 2025-01-01: Configured auto-renewal — prevents future expiration
     
     Project log entry (for _p014-site-rdsg-home.md):
     > 2025-01-01 14:30 - Update - Security hardening session
     > Created SSL renewal task and backup configuration.
```

**2. Offer to write (if filesystem available):**
```
LLM: Want me to add these to the files?
```

**3. Or provide for manual placement:**
```
LLM: Copy these into your task and project home doc when you're ready.
```

---

## What the System Tracks

Throughout the conversation:

| Tracked | Used For |
|---------|----------|
| Current track context | Primary track in log |
| Files created | Session summary |
| Files edited | Session summary |
| Decisions made | Key decisions list |
| AI contributions | Log entry |

---

## Primary Track Selection

If multiple tracks were touched, determine primary by:

1. **Explicit context** — Track opened with `!hi-[track]`
2. **Most activity** — Track with most files created/edited
3. **Most discussed** — Track mentioned most in conversation
4. **Ask** — If unclear, ask user

---

## Log Entry Format (RC Principle)

Always include *why*, not just *what*:

```
Good: "Updated SSL configuration — auto-renewal now prevents expiration"
Bad:  "Updated SSL configuration"
```

Types: decision, update, milestone, issue, note

---

## AI Contribution Section

Document what the assistant specifically contributed:

```
- AI contribution: 
  - Generated SSL renewal script with error handling
  - Validated backup configuration syntax
  - Suggested daily schedule based on usage patterns
```

Don't include:
- User actions
- Decisions made by user
- General conversation flow

---

## Output Behavior

After generating summary:

1. Display summary, task history entry, and log entry in conversation
2. Explain what each piece is for
3. Offer to write directly (if filesystem available) or provide download

```
LLM: ✓ Session wrapped up.
     
     Don't forget to add the log entry to your project home doc!
```

---

## Shortcut: !bye

For experienced users who want to skip the conversation:

```
!bye
```

Immediately generates:
- Session summary
- Task history entry  
- Log entry

Skips the project status check prompt.

---

## Multi-Day Sessions

When a session spans multiple calendar days:

- Use **closing date** for all timestamps
- Note in summary: "Session spanned Dec 30 to Jan 1"
- Single session log file with closing date

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| No session context | Generate summary from conversation analysis |
| No tracks touched | "General session — no specific project context" |
| Can't determine primary track | Ask user which track to log to |
| Write fails | Fall back to download, show content |
