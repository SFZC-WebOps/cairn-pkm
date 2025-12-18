# !bye - Session Closure
*Type: Display | Version: 3.1 | Updated: 2025-12-16*

## Quick Reference

| Action | What Happens | Permission |
|--------|--------------|------------|
| `!bye` | Analyzes session, generates task history + log entry | None |

Context is derived from previous `!hi-[target]` or detected from conversation.
User manually copies output to relevant locations.

---

## Requirements

**Assistant Capabilities:**
- Conversation analysis
- Timestamp generation
- Context tracking from session

**Vault Structure:**
- `Tracks/` — Projects and areas with home docs containing logs

---

## Execution

### Phase 0: DateTime
```
OUTPUT: "🕐 Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
OUTPUT: "Using this date/time for all log entries"
```

### Phase 1: Determine Context
```
PRIORITY:
1. stored_context from !hi-[target]
2. most_mentioned project/track in conversation
3. file_operations common path
4. default to "System" if unclear

RESULT: target_context = {
  type: "project" | "area",
  identifier: "track identifier",
  path: "{VAULT_PATH}/Tracks/{location}/"
}
```

### Phase 2: Analyze Session
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

### Phase 3: Task History Entry
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
═══════════════════════════════════════
{history_entry}

Copy this to the relevant task:
- If working on specific task: Add to that task's history
- If general work: Add to appropriate track catch-all task
═══════════════════════════════════════
```

### Phase 4: Display Log
```
FORMAT:
### {YYYY-MM-DD HH:MM} - {Type} - {Summary}
{Past_tense_description}
- Files: {action} {filenames}
- AI Contribution: {contribution}

OUTPUT:
📋 COPY THIS LOG ENTRY TO: {path}_*-home.md (Log section)
═══════════════════════════════════════
{formatted_entry}
═══════════════════════════════════════
```

### Phase 5: Completion
```
OUTPUT:
✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready to paste
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════

STOP
```

---

## Output Format

```
🕐 Current Date/Time: January 28, 2025 at 14:30 PST
Using this date/time for all log entries

📝 SUGGESTED TASK HISTORY ENTRY:
═══════════════════════════════════════
2025-01-28: Fixed !bye command to always generate task history entries

Copy this to the relevant task:
- If working on specific task: Add to that task's history
- If general work: Add to appropriate track catch-all task
═══════════════════════════════════════

📋 COPY THIS LOG ENTRY TO: {VAULT_PATH}/Tracks/area-system/_area-system-home.md (Log section)
═══════════════════════════════════════
### 2025-01-28 14:30 - Development - Updated !bye command specification
Modified command to always generate task history entries regardless of task mentions.
- Files: Created bye-session-closure-v2.md
- AI Contribution: Assistant restructured command logic for consistent output
═══════════════════════════════════════

✓ Session closed successfully
✓ Task history entry ready
✓ Log entry ready to paste
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
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
- Max 3 lines
- Focus on outcomes
- Combine similar actions

**AI Contributions:**
- Format: `Assistant {verb} {specific_contribution}`

---

## Error Handling

```
IF no_context:
  OUTPUT: "Assuming System area for this session"
  CONTINUE with System as context
```

---

## File Paths

| Purpose | Path |
|---------|------|
| Base | `{VAULT_PATH}/` |
| Tracks | `{VAULT_PATH}/Tracks/` |
| Log location | Track home doc (`_*-home.md`) Log section |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0 | 2025-01-28 | Always generates task history entry |
| 3.0 | 2025-12-15 | LLM-agnostic refactor |
| 3.1 | 2025-12-16 | Standardized format |
