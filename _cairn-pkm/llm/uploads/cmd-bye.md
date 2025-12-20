# !bye - Session Closure
*Type: Display/Write | Version: 0.8.1 | Updated: 2025-12-20*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!bye` | Summarizes session, generates task history + log entry, outputs session log |

Session context derived from `!hi` session tracking.

---

## Execution

### Phase 1: Display DateTime

Per `cmd-shared-patterns.md`

### Phase 2: Session Summary

```
READ: session context
  - tracks_viewed[]
  - files_created[]
  - files_edited[]
  - decisions[]
  - log_entries_added[]
  - change_entries_created[]

OUTPUT:
📊 SESSION SUMMARY
═══════════════════════════════════════════════

Tracks visited: [list]
Files created: [N] - [filenames]
Files edited: [N] - [filenames]
Log entries added: [N]
Changes documented: [N] - [change_ids]
Key decisions: [list]

═══════════════════════════════════════════════
```

### Phase 3: Analyze & Synthesize

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
CREATE: "{YYYY-MM-DD}: {problem_focused_summary}"

RULES:
- One line, <100 chars
- Problem/solution focused
- Technical, factual, past tense

OUTPUT:
📝 SUGGESTED TASK HISTORY ENTRY:
═══════════════════════════════════════════════
{history_entry}

Copy to relevant task or track catch-all task.
═══════════════════════════════════════════════
```

### Phase 5: Generate Log Entry

```
DETERMINE: primary_track (most files touched, most time spent, user focus)

FORMAT:
### {YYYY-MM-DD HH:MM} - {Type} - {Summary}
{Past_tense_description}
- Tracks: {primary_track} [+ others if applicable]
- Files: {action} {filenames}
- AI Contribution: 
   - {contribution}
- Change ID: {change_id if any}
```

### Phase 6: Output Session Log

```
GENERATE: filename = session-log-{YYYY-MM-DD}-{HHMMSS}.md
CONSTRUCT: filepath = {VAULT_PATH}/Capture/{filename}

CONTENT:
# Session Log: {YYYY-MM-DD}
Closed: {HH:MM} {TIMEZONE}

## Summary
{session summary}

## Task History Entry
{history_entry}

## Log Entry
Paste to: {VAULT_PATH}/Tracks/{primary_track}/_*-home.md (Log section)

---
{formatted_log_entry}
---

## Session Details
- Duration: [estimated]
- Commands used: [list]
- Tracks touched: [list]
- Changes documented: [list or "None"]

CALL: OUTPUT_FILE(filepath, content)
```

Output varies by file_operations setting. See `cmd-output-behavior.md`.

### Phase 7: Completion

```
OUTPUT:
✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready
✓ Session log created

═══════════════════════════════════════════════
🤖 Session ended. Start new session with !hi
═══════════════════════════════════════════════

CLEAR: session context
STOP
```

---

## Synthesis Rules

**Task History:**
- One line, <100 chars, technical
- Format: `YYYY-MM-DD: {what was fixed/done/resolved}`
- Examples:
  - `2025-01-28: Fixed SSL certificate chain validation issue`
  - `2025-01-28: Implemented CSV parser for task display`

**Log Entry:**
- Past tense, max 3 lines
- Focus on outcomes
- Combine similar actions

**AI Contributions:**
- Format: `Assistant {verb} {specific_contribution}`

**Primary Track Selection:**
1. Track where files were created/edited
2. Track with most log entries added
3. Track most discussed
4. First track opened

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No session context | Analyze conversation directly, note "No formal session detected" |
| No tracks touched | "General session - no specific track context" |
| Empty session | "No significant work detected this session" |

Common errors: See `cmd-shared-patterns.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.8.1 | 2025-12-20 | Fixed AI Contribution formatting - moved to nested bullet for better readability |
| 0.8.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.7.1 | 2025-12-19 | Updated references from !change to !changelog |
| 0.7.0 | 2025-12-19 | Reset to pre-release versioning |
