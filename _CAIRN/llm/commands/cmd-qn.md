# !qn - Quick Note
*Type: Write | Version: 3.1 | Updated: 2025-12-16*

## Quick Reference

| Command | What Happens | Permission |
|---------|--------------|------------|
| `!qn` | Capture from conversation | Confirmation required |
| `!qn [topic]` | Capture specific topic | Confirmation required |

**Workflow:** Type `!qn` → Review proposed content → Confirm → File created

---

## Requirements

**Assistant Capabilities:**
- File system write access
- Conversation context analysis
- Timestamp generation

**Vault Structure:**
- `Capture/` — Inbox for unprocessed items

---

## Execution

### Phase 1: Analyze
```
SOURCE: Current conversation context
EXTRACT:
  - Main topic discussed
  - Key decisions or findings
  - Action items if any
  - Related project/task references
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
- [ ] {Item 1}
- [ ] {Item 2}

## References
- Related track: {project/area if applicable}
- Related task: {task reference if applicable}
```

### Phase 3: Propose
```
OUTPUT: "I would create {filename} with:"
OUTPUT: {formatted_content}
OUTPUT: "Confirm to create"

WAIT FOR: confirmation
```

### Phase 4: Execute
```
ON confirmation:
  CREATE: {VAULT_PATH}/Capture/{filename}
  WRITE: {content}
```

### Phase 5: Completion
```
OUTPUT:
✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

---

## Filename Rules

**Format:** `qn-YYYY-MM-DD-HHMMSS-{slug}.md`

**Slug generation:**
- Max 3-4 words
- Lowercase, hyphens
- Descriptive of content

**Examples:**
- `qn-2025-09-26-143022-ssl-cert-fix.md`
- `qn-2025-09-26-151545-meeting-notes.md`
- `qn-2025-09-26-160130-deployment-steps.md`

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation empty | "No context to capture. What should I note?" |
| Write fails | "Failed to create: {error}" — Offer retry |

---

## File Paths

| Purpose | Path |
|---------|------|
| Output | `{VAULT_PATH}/Capture/` |
| Pattern | `qn-*.md` |
| Processing | Manual during reviews |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.1 | 2025-09-26 | Previous version |
| 3.0 | 2025-12-15 | LLM-agnostic refactor |
| 3.1 | 2025-12-16 | Standardized format |
