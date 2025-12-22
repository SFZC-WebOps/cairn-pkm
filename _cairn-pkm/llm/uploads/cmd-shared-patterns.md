# Shared Command Patterns
*Type: Include | Updated: 2025-12-22*

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

**VBM Examples for All Write Operations:**

```
BEFORE task creation:
  VERIFY: Track context is correct (p014-blog-migr)
  VERIFY: tasks/ directory exists
  STATE: "Creating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/"
  EXECUTE: Write operation
  VERIFY: File created successfully

BEFORE task edit:
  VERIFY: Target file exists (20251220-ssl-fix.md found in tasks/)
  VERIFY: File is parseable (YAML frontmatter valid)
  STATE: "Updating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/"
  EXECUTE: Write operation
  VERIFY: File updated successfully

BEFORE area creation:
  VERIFY: area-{domain} does not already exist
  STATE: "Creating Tracks/area-facilities/"
  EXECUTE: mkdir, file creation
  VERIFY: Directory and home doc created

BEFORE object creation:
  VERIFY: Placement decision confirmed (Objects/ or track/resources/)
  VERIFY: Target directory exists
  STATE: "Creating contact-jennifer-martinez.md in Tracks/p003-facl-hvac/resources/"
  EXECUTE: Write operation
  VERIFY: File created successfully
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

**Purpose:** Future-you (and collaborators) understand decision context.

---

## Rationale Capture (RC) Principle - Usage Guide

RC principle applies to these entry types:

### Task History

**Format:** `YYYY-MM-DD: {action} - {reason}`

**Required:** Always include reason

**Examples:**
- "2025-12-21: Increased priority - blocking launch"
- "2025-12-21: Set viz to blocked - waiting on vendor"
- "2025-12-21: Updated due date - aligned with project deadline"
- "2025-12-21: Marked complete - verified in production"

### Changelog

**Field:** `rationale: "{why change was necessary}"`

**Required:** Always populate

**Examples:**
- `rationale: "Certificates expiring caused service outage"`
- `rationale: "Performance degradation impacting user experience"`
- `rationale: "Security audit requirement"`
- `rationale: "Project dependency for Q2 launch"`

### Log Entries (Project/Area)

**Format:** `Type - {what} - {why/context}`

**Recommended:** Include context when non-obvious

**Examples:**
- "Update - Email template assigned - needed for Jan 15 launch"
- "Decision - Switching to quarterly reviews - monthly too frequent"
- "Milestone - Migration complete - ready for testing"
- "Issue - SSL certificate expired - renewed and monitoring added"

### When RC is Optional

- Simple status updates ("Setup", "Closed", "Created")
- Routine maintenance logs
- Self-explanatory actions
- Obvious outcomes

**Examples where RC not needed:**
- "Setup - Area created"
- "Closed - Session ended"
- "Created - Task added"

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

**Note on User Preferences File Locations:**

The preferences file exists in two contexts:

1. **Claude Project Context:** `/mnt/project/cairn-pkm-user-prefs.yaml`
   - This is where Claude reads the file when uploaded to a Claude project
   - Upload your customized prefs here for Claude to use

2. **Vault Storage:** `_local/cairn-pkm-user-prefs.yaml`
   - This is where the file lives in your Obsidian vault
   - Edit this file to customize your preferences
   - Keep this synchronized with what you upload to Claude

**Workflow:** Edit `_local/cairn-pkm-user-prefs.yaml` in your vault, then upload a copy to your Claude project as `/mnt/project/cairn-pkm-user-prefs.yaml`.

---

## DateTime Output

When command needs to display current time:

```
OUTPUT: "📋═ Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
```

---

## Completion Pattern

Standard ending for all commands:

```
OUTPUT:
✓ Task complete
═══════════════════════════════════════════════
📋Â¤"“ Waiting for next instruction
═══════════════════════════════════════════════

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
| Unknown command | "Unknown command. Try !help" |
| cairn-pkm-user-prefs.yaml missing | Use defaults, continue |
| Write fails (write/confirm mode) | Report error, fall back per GFC |
| Download fails | Report error, fall back to display per GFC |
| Google Drive not connected | Warn user, fall back to display per GFC |

**GFC messaging pattern:**
```
⚠ ️ {operation} failed: {reason}
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

### Encoding Verification

All commands that create or edit files must apply ftfy encoding fix:

**Standard pattern:**
```python
import ftfy

# Before writing/presenting file
content = ftfy.fix_text(content)

# Then write or display
```

**Purpose:** Prevents mojibake characters (garbled text like Ã, â€, etc.) from appearing in markdown files.

**When to apply:**
- After generating any file content
- Before writing to filesystem
- Before displaying to user
- After reading files for editing

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
