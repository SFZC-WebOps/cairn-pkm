# !bye - Session Closure
*Type: Display | Version: 4.0 | Updated: 2025-12-19*

## Quick Reference

| Action | What Happens |
|--------|--------------|
| `!bye` | Summarizes session, generates task history + log entry, presents as download |

Session context is derived from `!hi` session tracking.

---

## Requirements

**Assistant Capabilities:**
- Conversation analysis
- Session context access
- Timestamp generation
- File generation and presentation

**Vault Structure:**
- `Tracks/` — Projects and areas with home docs containing logs

---

## Execution

### Phase 1: DateTime
```
OUTPUT: "🕐 Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
```

### Phase 2: Session Summary
```
READ: session context
  - tracks_viewed[]
  - files_created[]
  - files_edited[]
  - decisions[]
  - log_entries_added[]

OUTPUT:
📊 SESSION SUMMARY
═══════════════════════════════════════════════

Tracks visited:
- [track 1]
- [track 2]

Files created: [N]
- [filename 1]
- [filename 2]

Files edited: [N]
- [filename 1]
- [filename 2]

Log entries added: [N]

Key decisions:
- [decision 1]
- [decision 2]

═══════════════════════════════════════════════
```

### Phase 3: Analyze Session
```
SCAN FOR:
- file_operations: created/modified/deleted
- commands_used: commands executed
- decisions: finalized decisions
- problems: resolved issues
- ai_contributions: assistant contributions
- technical_work: development/configuration/documentation

SYNTHESIZE: Problem-focused summary of session
```

### Phase 4: Task History Entry
```
CREATE: history_entry = "{YYYY-MM-DD}: {problem_focused_summary}"

RULES:
- One line, <100 chars
- Problem/solution focused
- Technical, factual
- Past tense
- No fluff

OUTPUT:
📝 SUGGESTED TASK HISTORY ENTRY:
═══════════════════════════════════════════════
{history_entry}

Copy this to the relevant task:
- If working on specific task: Add to that task's history
- If general work: Add to appropriate track catch-all task
═══════════════════════════════════════════════
```

### Phase 5: Generate Log Entry
```
DETERMINE: primary_track = most significant track from session
  (based on: most files touched, most time spent, user focus)

IF multiple tracks touched:
  INCLUDE: "Also: [other tracks]" in log entry

FORMAT:
### {YYYY-MM-DD HH:MM} - {Type} - {Summary}
{Past_tense_description}
- Tracks: {primary_track} [+ others if applicable]
- Files: {action} {filenames}
- AI Contribution: {contribution}
```

### Phase 6: Create Download File
```
GENERATE: filename = session-log-{YYYY-MM-DD}-{HHMMSS}.md
CREATE: file content with:

# Session Log: {YYYY-MM-DD}
Closed: {HH:MM} {TIMEZONE}

## Summary
{session summary from Phase 2}

## Task History Entry
{history_entry}

## Log Entry
Paste to: {VAULT_PATH}/Tracks/{primary_track}/_*-home.md (Log section)

---

{formatted_log_entry}

---

## Session Details
- Duration: [estimated from conversation]
- Commands used: [list]
- Tracks touched: [list]

PRESENT: file for download using present_files
```

### Phase 7: Completion
```
OUTPUT:
✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready

📥 Download your session log above

═══════════════════════════════════════════════
🤖 Session ended. Start new session with !hi
═══════════════════════════════════════════════

CLEAR: session context
STOP
```

---

## Synthesis Rules

**Task History:**
- Always generate based on session work
- One line, technical summary
- Format: `YYYY-MM-DD: {what was fixed/done/resolved}`
- Examples:
  - `2025-01-28: Fixed SSL certificate chain validation issue`
  - `2025-01-28: Implemented CSV parser for task display`

**Log Entry:**
- Past tense only
- Max 3 lines description
- Focus on outcomes
- Combine similar actions
- Reference all tracks if multiple were touched

**AI Contributions:**
- Format: `Assistant {verb} {specific_contribution}`

**Primary Track Selection:**
When session touched multiple tracks, select primary by:
1. Track where files were created/edited
2. Track with most log entries added
3. Track most discussed
4. First track opened

---

## Output Example

```
🕐 Current Date/Time: December 19, 2025 at 14:30 PST

📊 SESSION SUMMARY
═══════════════════════════════════════════════

Tracks visited:
- p014-stor-migr
- area-webops

Files created: 2
- 20251219-ssl-cert-renewal.md
- 20251219-backup-validation.md

Files edited: 1
- 20251215-storage-audit.md

Log entries added: 1

Key decisions:
- Chose S3 over local storage for backups
- Scheduled migration for January maintenance window

═══════════════════════════════════════════════

📝 SUGGESTED TASK HISTORY ENTRY:
═══════════════════════════════════════════════
2025-12-19: Created SSL renewal and backup validation tasks; decided on S3 storage

Copy this to the relevant task:
- If working on specific task: Add to that task's history
- If general work: Add to appropriate track catch-all task
═══════════════════════════════════════════════

📥 [session-log-2025-12-19-143022.md available for download]

✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready

📥 Download your session log above

═══════════════════════════════════════════════
🤖 Session ended. Start new session with !hi
═══════════════════════════════════════════════
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No session context | Analyze conversation directly, note "No formal session detected" |
| No tracks touched | "General session - no specific track context" |
| Empty session | "No significant work detected this session" |

---

## File Paths

| Purpose | Path |
|---------|------|
| Download | `/mnt/user-data/outputs/session-log-{timestamp}.md` |
| Log destination | `{VAULT_PATH}/Tracks/{track}/_*-home.md` (Log section) |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 3.1 | 2025-12-16 | Previous version |
| 4.0 | 2025-12-19 | Added session summary, multi-track support, downloadable output file |
