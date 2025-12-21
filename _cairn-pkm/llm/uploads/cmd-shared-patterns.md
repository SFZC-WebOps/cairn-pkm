# Shared Command Patterns
*Type: Include | Updated: 2025-12-21*

Reference document for patterns used across multiple commands. Commands should reference this rather than duplicating.

---

## Robustness Principles

Three principles inform all command implementations. These derive from systems engineering practices for reliable operations.

### Verify-Before-Modify (VBM)

Before any write operation, confirm state matches expectations:

```
BEFORE file write:
  VERIFY: target_path status (exists | not_exists) matches intent
  VERIFY: current_track context is correct
  STATE: "{operation} {filename} in {path}"
  THEN: execute operation
```

**Purpose:** Catches wrong-target writes and context confusion before they happen.

**Example output:**
```
Creating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/
```

### Graceful Fallback Chain (GFC)

When operations fail, degrade gracefully with user visibility:

```
ON FAILURE:
  1. Attempt next fallback mode in chain
  2. Report current position in fallback chain
  3. Preserve and display content
  4. NEVER lose content silently
```

**Fallback chain:** write → download → display

**Purpose:** User never loses work due to system/permission errors.

See `cmd-output-behavior.md` for implementation details.

### Rationale Capture (RC)

Log entries should capture *why*, not just *what*:

```
GOOD: "2025-12-20: Deferred migration - waiting on vendor API access"
BAD:  "2025-12-20: Deferred migration"

GOOD: rationale: "Certificates expiring caused service outage"
BAD:  (no rationale field)
```

**Formats:**
- Task history: `YYYY-MM-DD: {action} - {reason}`
- Changelog: Include `rationale:` field
- Log entries: Context for future reference

**Purpose:** Future-you (and collaborators) understand decision context.

---

## Initialization Pattern

All commands that use user preferences:

```
READ: /mnt/project/cairn-pkm-user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - timezone (default: "America/Los_Angeles")
  - default_assignee (default: "")

ON FILE NOT FOUND: Use defaults, continue execution
```

---

## DateTime Output

When command needs to display current time:

```
OUTPUT: "🕐 Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
```

---

## Completion Pattern

Standard ending for all commands:

```
OUTPUT:
✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════

STOP
```

---

## File Output

All file creation/editing uses the OUTPUT_FILE pattern from `cmd-output-behavior.md`.

```
APPLY: VBM principle (verify target, state operation)
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}
CALL: OUTPUT_FILE(filepath, content)
ON FAILURE: GFC principle (fallback chain with visibility)
```

Output varies by `file_operations` setting (display/download/write/confirm). See `cmd-output-behavior.md` for behavior details and fallback handling.

---

## Common Error Handling

These errors apply to all commands unless overridden:

| Situation | Response |
|-----------|----------|
| cairn-pkm-user-prefs.yaml missing | Use defaults, continue |
| Write fails (write/confirm mode) | Report error, fall back per GFC |
| Download fails | Report error, fall back to display per GFC |
| Google Drive not connected | Warn user, fall back to display per GFC |

**GFC messaging pattern:**
```
⚠ {operation} failed: {reason}
↳ Falling back to {fallback_mode}
↳ Content preserved below
```

---

## Encoding Requirement

Before presenting any markdown file to user:

```python
import ftfy
content = ftfy.fix_text(content)
```

This prevents mojibake (double-encoded UTF-8). Non-negotiable.

---

## Vault Path Reference

| Alias | Meaning |
|-------|---------|
| `{VAULT_PATH}` | Root of user's Obsidian vault |
| `{track}` | Current area or project folder name |

Common paths:
- Tasks: `{VAULT_PATH}/Tracks/{track}/tasks/`
- Resources: `{VAULT_PATH}/Tracks/{track}/resources/`
- Capture: `{VAULT_PATH}/Capture/`
- Objects: `{VAULT_PATH}/Objects/`
- Changelog: `{VAULT_PATH}/_local/data/changelog/`
