# !quicknote - Quick Note
*Type: Read/Write | Updated: 2025-12-21*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!quicknote` | Capture from conversation context |
| `!quicknote [topic]` | Capture specific topic |

**Workflow:** `!quicknote` → Review proposed content → Confirm → Output per prefs

---

## When to Use

**Use !quicknote for:**
- Capturing insights from conversation (not action-oriented)
- Recording research findings or discoveries
- Documenting decisions without immediate tasks
- Preserving reference information discussed
- Noting ideas or concepts for later processing

**Use !create task instead for:**
- Action-oriented work items
- Things with due dates or assignees
- Work that needs tracking and status updates
- Items with subtasks or dependencies
- Anything requiring priority/phase/effort tracking

**Examples:**

| Situation | Command | Why |
|-----------|---------|-----|
| "We discussed HVAC options" | !quicknote | Reference info, no action |
| "Create SSL renewal script" | !create task | Action item with clear deliverable |
| "Found interesting API docs" | !quicknote | Research finding, no immediate action |
| "Schedule board meeting" | !create task | Action with due date |
| "Claude explained template discovery order" | !quicknote | Concept learned, for reference |
| "Fix broken deployment pipeline" | !create task | Work that needs doing |

**Workflow integration:**
- `!quicknote` during exploration/research
- Process captures later via `!create task` if action needed
- Keep notes for reference in Capture/ folder
- Use daily/weekly reviews to convert notes → tasks

---

## Execution

**Initialize:** Per `cmd-shared-patterns.md`

### Phase 1: Analyze
```
SOURCE: Current conversation context
EXTRACT:
  - Main topic discussed
  - Key decisions or findings
  - Action items (only if NOT better as !create task)
  - Related track references

DETECT context type:
  IF conversation was about creating task:
    - Skip redundant action items
    - Note: "Task created separately via !create"
  IF conversation includes clear action items:
    - Note: "Consider !create task for action items"
  ELSE:
    - Include action items if present
```

### Phase 2: Generate
```
FILENAME: qn-{YYYY}-{MM}-{DD}-{HHMMSS}-{topic_slug}.md
PATH: {VAULT_PATH}/Capture/{filename}

CONTENT:
# Quick Note: {Topic}
Created: {YYYY-MM-DD HH:MM:SS}

## Context
{What prompted this note}

## Content
{Main content from discussion}

## Action Items (if any)
[Only include if NOT already handled by !create task]
- [ ] {Item}

## References
- Related track: {if applicable}
```

### Phase 3: Propose & Confirm
```
OUTPUT: "I would create {filename} with:"
OUTPUT: {formatted_content}

IF action items suggested AND context is task-creation:
  OUTPUT: "Note: Action items already captured via !create task"
  
OUTPUT: "Confirm to create"
WAIT FOR: confirmation
```

### Phase 4: Output
```
ON confirmation:
  CALL: OUTPUT_FILE(filepath, content)
  # See cmd-output-behavior.md for mode-specific behavior
```

**Complete:** Per `cmd-shared-patterns.md`

---

## Filename Rules

**Format:** `qn-YYYY-MM-DD-HHMMSS-{slug}.md`

**Slug:** Max 3-4 words, lowercase, hyphenated, descriptive

Examples:
- `qn-2025-09-26-143022-ssl-cert-fix.md`
- `qn-2025-09-26-160130-deployment-steps.md`

---

## Workflow Integration Examples

**Example 1: Research session**
```
User: [asks about HVAC system options]
Claude: [explains different HVAC types]
User: !quicknote

Result: qn-2025-12-21-143022-hvac-system-options.md
- Captures: Research findings, pros/cons
- No action items (informational)
```

**Example 2: Decision documentation**
```
User: [discusses switching to quarterly reviews]
Claude: [provides analysis]
User: !quicknote

Result: qn-2025-12-21-150045-quarterly-review-decision.md
- Captures: Decision rationale, considerations
- May include: Action item to update area config
```

**Example 3: Concept learning**
```
User: Explain how template discovery works
Claude: [explains priority order]
User: !quicknote

Result: qn-2025-12-21-153000-template-discovery-order.md
- Captures: Concept explanation
- No action items (reference material)
```

**Example 4: Avoiding redundancy**
```
User: !create task [creates email template task]
User: !quicknote

Result: qn-2025-12-21-160000-email-template-discussion.md
- Captures: Conversation context
- Skips: Redundant "create task" action item
- Notes: "Task created separately via !create"
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation empty | "No context to capture. What should I note?" |
| Context is pure task creation | "Task already created. Quicknote would be redundant. Continue?" |

Common errors: See `cmd-shared-patterns.md`
