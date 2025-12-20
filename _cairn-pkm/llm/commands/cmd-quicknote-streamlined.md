# !quicknote - Quick Note
*Type: Read/Write | Version: 0.7.0 | Updated: 2025-12-19*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!quicknote` | Capture from conversation context |
| `!quicknote [topic]` | Capture specific topic |

**Workflow:** `!quicknote` → Review proposed content → Confirm → Output per prefs

---

## Execution

**Initialize:** Per `cmd-shared-patterns.md`

### Phase 1: Analyze
```
SOURCE: Current conversation context
EXTRACT:
  - Main topic discussed
  - Key decisions or findings
  - Action items (if any)
  - Related track references
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
- [ ] {Item}

## References
- Related track: {if applicable}
```

### Phase 3: Propose & Confirm
```
OUTPUT: "I would create {filename} with:"
OUTPUT: {formatted_content}
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

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation empty | "No context to capture. What should I note?" |

Common errors: See `cmd-shared-patterns.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.7.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.6.1 | 2025-12-19 | Renamed from !qn to !quicknote |
| 0.6.0 | 2025-12-19 | Reset to pre-release versioning |
