# !bye - Session Closure
*Type: Display/Write | Updated: 2025-12-25*

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
ANALYZE conversation for:
  - tracks_viewed
  - files_created
  - files_edited
  - decisions
  - log_entries_added
  - change_entries_created

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

### Phase 4: Task History Entry (with Rationale)

```
CREATE: "{YYYY-MM-DD}: {action} - {rationale}"

RULES:
- One line, <100 chars
- Format: "action - reason" (RC principle)
- Technical, factual, past tense

EXAMPLES:
  GOOD: "2025-12-20: Fixed SSL chain - certs were expiring"
  GOOD: "2025-12-20: Deferred migration - waiting on API access"
  BAD:  "2025-12-20: Worked on SSL stuff"

OUTPUT:
📝 SUGGESTED TASK HISTORY ENTRY:
═══════════════════════════════════════════════
{history_entry}

Copy to relevant task or track catch-all task.
═══════════════════════════════════════════════
```

### Phase 5: Project Status Check

If primary track is a project (p###-*), prompt user to review and optionally update project state:

```
IF primary_track matches p###-*:
  READ: project home document frontmatter
  
  OUTPUT:
  📊 PROJECT STATUS CHECK
  ═══════════════════════════════════════════════
  Project: {project_id}
  
  Current state:
  - Status: {status}
  - Progress: {progress}%
  - Summary: {summary or "(not set)"}
  ═══════════════════════════════════════════════
  
  Update any of these? (enter field=value, or 'skip' to continue)
  Examples: progress=75, status=blocked, summary=Waiting on vendor response
  
  WAIT FOR: response
  
  SWITCH response:
    CASE "skip" | "no" | "n" | "":
      CONTINUE to Phase 6
    CASE contains "=":
      PARSE: field=value pairs (comma or newline separated)
      VALIDATE: 
        - status: planning | active | onhold | complete | archived
        - progress: 0-100
        - summary: any text
      UPDATE: frontmatter fields
      UPDATE: modified date
      OUTPUT: "✓ Updated {fields}"
      CONTINUE to Phase 6
    DEFAULT:
      OUTPUT: "Format: field=value (e.g., progress=75)"
      REPEAT prompt
```

### Phase 6: Generate Log Entry

```
DETERMINE: primary_track (see Primary Track Selection below)

FORMAT:
### {YYYY-MM-DD HH:MM} - {Type} - {Summary}
{Past_tense_description}
- Tracks: {primary_track} [+ others if applicable]
- Files: {action} {filenames}
- AI Contribution: 
   - {contribution}
- Change ID: {change_id if any}
```

### Phase 7: Output Session Log

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
- Commands used: [list]
- Tracks touched: [list]
- Changes documented: [list or "None"]

CALL: OUTPUT_FILE(filepath, content)
```

Output varies by file_operations setting. See `cmd-output-behavior.md`.

### Phase 8: Completion

```
OUTPUT:
✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready
✓ Session log created

═══════════════════════════════════════════════
🤖 Session ended. Start new session with !hi
═══════════════════════════════════════════════

STOP
```

---

## Synthesis Rules

**Task History (with RC - Rationale Capture):**
- One line, <100 chars, technical
- Format: `YYYY-MM-DD: {action} - {reason}`
- The "- {reason}" clause captures rationale
- Examples:
  - `2025-01-28: Fixed SSL certificate chain - validation was failing on renewal`
  - `2025-01-28: Implemented CSV parser - needed for task dashboard display`
  - `2025-01-28: Deferred API integration - waiting on auth documentation`

**Log Entry:**
- Past tense, max 3 lines
- Focus on outcomes
- Combine similar actions

---

## Primary Track Selection Logic

Determine which track should receive the log entry using this priority order:

### 1. Explicit !hi Session Context (Highest Priority)

If user opened track with `!hi-[target]`:
- **Use:** That track as primary
- **Reason:** Explicit user intent

Example: User ran `!hi-p002-prod-lnch` then created a task
→ Primary track: `p002-prod-lnch`

### 2. File Operations

If files were created or edited:
- **Count:** Files per track
- **Use:** Track with most files modified
- **Tie:** Track where most recent file was created/edited

Example: Created 2 files in p003-facl-hvac, 1 in area-facilities
→ Primary track: `p003-facl-hvac`

### 3. Track Discussion

If no files but tracks were discussed:
- **Use:** Track most mentioned in conversation
- **Fallback:** First track mentioned

Example: Discussed p002 extensively, mentioned area-admin once
→ Primary track: `p002-prod-lnch`

### 4. No Clear Track

If session had no track focus:
- **Use:** "General session - no specific track context"
- **Note:** This in the log entry
- **User action:** Manually place in appropriate track if desired

Example: General planning discussion, no files created
→ Primary track: None (user decides where to log)

### Edge Cases

**Multiple tracks equal weight:**
- Use the first one opened/mentioned
- List all tracks in log entry

**Track created during session:**
- New track is usually the primary (creation = focus)

**Track deleted/renamed during session:**
- Use most recent valid track name

**Multi-day sessions:**
When a session spans multiple calendar days (e.g., started Monday, closing Wednesday):
- Use the **closing date/time** for all timestamps
- Session log filename uses closing date: `session-log-{closing-date}-{HHMMSS}.md`
- Task history entry uses closing date
- Log entry timestamp uses closing date/time
- If work spanned multiple days, note in summary: "Session spanned {start-date} to {end-date}"
- For sessions spanning 3+ days, consider breaking into logical segments in the summary

---

## Log Entry Destination Guidance

The session log provides a formatted entry for pasting into the track's home document.

### Where to Paste

**Single track session:**
```
Paste to: {VAULT_PATH}/Tracks/{primary_track}/_*-home.md

Location within file: ## Log section
Position: Add as newest entry (top of log entries)
```

**Multiple tracks session:**
```
Primary destination: Track with most activity
Optional: Copy log to other touched tracks (mention primary in entry)

Format for secondary tracks:
### {YYYY-MM-DD HH:MM} - Update - Session work
Session primarily in p003-facl-hvac. Created [file] for this track.
- Primary session log: See p003-facl-hvac home doc
```

**No clear track:**
```
Option 1: File in Capture/ only (no track log entry)
Option 2: Add to area-admin or personal tracking area
Option 3: Distribute to relevant tracks as noted
```

### Log Entry Structure

Standard format generated by !bye:

```markdown
### YYYY-MM-DD HH:MM - {Type} - {Summary}
{Description of work in past tense, 1-3 sentences}
- Tracks: {primary} [+ secondary if applicable]
- Files: Created {filenames} [Edited {filenames}]
- AI Contribution:
   - {specific contributions}
- Change ID: {change_id if documented via !changelog}
```

**Type values:**
- Setup - Initial creation/configuration
- Update - General work session
- Decision - Decision made
- Milestone - Significant completion
- Issue - Problem addressed

---

## AI Contribution vs General Notes

Distinguish between AI-specific contributions and general session notes.

### AI Contribution Section

**Include here:** Things the AI assistant specifically did

**Format:** `- {specific_contribution}`

**Examples:**
- "Generated SSL renewal script with error handling"
- "Debugged HVAC configuration file syntax"
- "Researched project naming conventions"
- "Created YAML template for device objects"
- "Validated task hierarchy structure"

**Don't include:**
- User actions
- Decisions made by user
- General session flow
- Commands executed

### General Description

**Include in main log entry:** Overall session narrative

**Examples:**
- "Created new facilities area and HVAC project structure"
- "Configured device and contact object templates"
- "Planned Q1 launch with task breakdown"

### Examples of Proper Separation

**Example 1: File creation session**
```markdown
### 2025-12-21 15:30 - Setup - New facilities tracking

Created area-facilities and p003-facl-hvac project with complete folder structure. 
Set up device and contact object templates for HVAC vendor tracking.

- Tracks: p003-facl-hvac + area-facilities
- Files: Created 8 (area home, project home, 2 objects, 4 tasks)
- AI Contribution:
   - Generated project naming convention recommendations
   - Created object templates for device and contact types
   - Validated folder structure against architecture
```

**Example 2: Bug fix session**
```markdown
### 2025-12-21 16:45 - Update - SSL certificate renewal

Fixed SSL certificate validation issue causing deployment failures. 
Implemented automated renewal script with monitoring.

- Tracks: p002-prod-lnch
- Files: Edited ssl-renewal.sh, Created monitoring.yaml
- AI Contribution:
   - Debugged certificate chain validation logic
   - Generated renewal script with error handling
   - Added monitoring configuration
- Change ID: CHG-20251221-164530
```

**What NOT to do:**
```markdown
❌ BAD - AI contribution too vague:
- AI Contribution:
   - Helped with the session
   - Answered questions

❌ BAD - User actions in AI contribution:
- AI Contribution:
   - User created area-facilities
   - Session involved planning

✓ GOOD - Specific AI contributions only:
- AI Contribution:
   - Generated folder structure commands
   - Created object template examples
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No session context | Analyze conversation directly, note "No formal session detected" |
| No tracks touched | "General session - no specific track context" |
| Empty session | Note in summary, proceed with close |
| Output failure | Fall back per GFC (see cmd-output-behavior.md) |

Common errors: See `cmd-shared-patterns.md`
