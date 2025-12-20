# Shared Command Patterns
*Type: Include | Version: 0.1.0 | Updated: 2025-12-19*

Reference document for patterns used across multiple commands. Commands should reference this rather than duplicating.

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
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}
CALL: OUTPUT_FILE(filepath, content)
```

Output varies by `file_operations` setting (display/download/write/confirm). See `cmd-output-behavior.md` for behavior details and examples.

---

## Common Error Handling

These errors apply to all commands unless overridden:

| Situation | Response |
|-----------|----------|
| cairn-pkm-user-prefs.yaml missing | Use defaults, continue |
| Write fails (write/confirm mode) | Report error, fall back to display |
| Download fails | Report error, fall back to display |
| Google Drive not connected | Warn user, fall back to display |

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

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.1.0 | 2025-12-19 | Initial creation - consolidated from cmd-* files |
