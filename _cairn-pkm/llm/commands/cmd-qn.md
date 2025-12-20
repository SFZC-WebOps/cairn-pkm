# !qn - Quick Note
*Type: Read/Write | Version: 0.6.0 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | What Happens | Output Mode |
|---------|--------------|-------------|
| `!qn` | Capture from conversation | Per user-prefs.yaml |
| `!qn [topic]` | Capture specific topic | Per user-prefs.yaml |

**Workflow:** Type `!qn` â†’ Review proposed content â†’ Confirm â†’ Output per prefs

---

## Requirements

**Assistant Capabilities:**
- File system access (if file_operations = write or confirm)
- Conversation context analysis
- Timestamp generation

**User Configuration:**
- `_local/user-prefs.yaml` â€” file_operations setting, timezone

**Vault Structure:**
- `Capture/` â€” Inbox for unprocessed items

---

## Initialization

```
READ: {VAULT_PATH}/_local/user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - timezone (default: "America/Los_Angeles")
```

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

### Phase 4: Output
```
ON confirmation:
  CONSTRUCT: filepath = {VAULT_PATH}/Capture/{filename}
  CONSTRUCT: content = {note content}
  
  CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

### Phase 5: Completion
```
OUTPUT:
âœ“ Task complete
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸ¤– Waiting for next instruction
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
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

## Output Examples

### Proposal Phase

```
I would create qn-2025-12-18-143022-api-integration-notes.md with:

# Quick Note: API Integration Notes
Created: 2025-12-18 14:30:22

## Context
Discussion about integrating the new payment API.

## Content
Key points from our conversation:
- API uses OAuth 2.0 for authentication
- Rate limit is 100 requests per minute
- Webhook support available for async notifications

## Action Items
- [ ] Request API credentials from vendor
- [ ] Review API documentation

## References
- Related track: p045-payment-integration

Confirm to create
```

### Completion (display mode)

```
ðŸ“„ FILE CONTENT
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
Filename: qn-2025-12-18-143022-api-integration-notes.md
Path: {VAULT_PATH}/Capture/

{complete file content}

â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
Copy this content and save to the path above.

âœ“ Task complete
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸ¤– Waiting for next instruction
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
```

### Completion (write mode)

```
âœ“ Created {VAULT_PATH}/Capture/qn-2025-12-18-143022-api-integration-notes.md

âœ“ Task complete
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸ¤– Waiting for next instruction
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
```

### Completion (confirm mode)

```
ðŸ“„ PROPOSED FILE
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
Filename: qn-2025-12-18-143022-api-integration-notes.md
Path: {VAULT_PATH}/Capture/

{complete file content}

â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
Write this file? (yes/no)
```

Then on confirmation:

```
âœ“ Created {VAULT_PATH}/Capture/qn-2025-12-18-143022-api-integration-notes.md

âœ“ Task complete
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸ¤– Waiting for next instruction
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation empty | "No context to capture. What should I note?" |
| user-prefs.yaml missing | Use defaults: display mode, local target |
| Write fails (write/confirm mode) | Report error, fall back to display mode |

---

## File Paths

| Purpose | Path |
|---------|------|
| User prefs | `{VAULT_PATH}/_local/user-prefs.yaml` |
| Output | `{VAULT_PATH}/Capture/` |
| Pattern | `qn-*.md` |
| Processing | Manual during reviews |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.6.0 | 2025-12-19 | Reset to pre-release versioning (was 4.0) |
| 4.0 | 2025-12-18 | Added user-prefs support, configurable output mode |
| 3.1 | 2025-12-16 | Previous version (display only) |
