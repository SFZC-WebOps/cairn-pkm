# Output Behavior Pattern
*Type: Include | Version: 0.4.0 | Updated: 2025-12-19*

Shared pattern for file output operations. Commands reference this rather than duplicating logic.

---

## Configuration

Read from `/mnt/project/cairn-pkm-user-prefs.yaml`:

```yaml
file_operations: "display"    # display | download | write | confirm
write_target: "local"         # local | gdrive
gdrive_vault_path: ""         # Required if write_target is gdrive
```

---

## Mode Comparison

| Mode | How It Works | Best For |
|------|--------------|----------|
| `display` | Shows content to copy/paste | Manual control, any environment |
| `download` | Creates downloadable file | Web-based LLM (Claude.ai) |
| `write` | Writes directly to target | Desktop app, MCP, Google Drive |
| `confirm` | Shows content, asks, then writes | Review before writing |

---

## Pattern Definition

```
OUTPUT_FILE(filepath, content):

  # Apply encoding fix (see cmd-shared-patterns.md)
  content = ftfy.fix_text(content)

  READ: file_operations, write_target from prefs
  DEFAULTS: file_operations = "display", write_target = "local"

  SWITCH file_operations:

    CASE "display":
      OUTPUT: "📄 FILE CONTENT"
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Filename: {filename}"
      OUTPUT: "Path: {filepath}"
      OUTPUT: ""
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Copy this content and save to the path above."

    CASE "download":
      output_path = /mnt/user-data/outputs/{filename}
      WRITE: content to output_path
      CALL: present_files([output_path])
      OUTPUT: "Download the file above and save to: {filepath}"

    CASE "confirm":
      OUTPUT: "📄 PROPOSED FILE"
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Write this file? (yes/no)"
      IF confirmed: CALL WRITE_TO_TARGET(filepath, content)
      ELSE: OUTPUT "File not written."

    CASE "write":
      CALL: WRITE_TO_TARGET(filepath, content)


WRITE_TO_TARGET(filepath, content):

  SWITCH write_target:

    CASE "local":
      CREATE: directory if not exists
      WRITE: content to filepath
      OUTPUT: "✓ Created {filepath}"

    CASE "gdrive":
      IF gdrive_vault_path empty: FALLBACK to display
      gdrive_path = gdrive_vault_path + "/" + relative_path
      CALL: google_drive_create_or_update(gdrive_path, content)
      OUTPUT: "✓ Created {gdrive_path} in Google Drive"
```

---

## Usage in Commands

```
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}
CALL: OUTPUT_FILE(filepath, content)
```

---

## Error Handling & Fallbacks

| Situation | Response |
|-----------|----------|
| prefs file missing | Use display mode |
| Invalid file_operations | Use display mode, warn |
| Invalid write_target | Use local, warn |
| gdrive_vault_path empty | Warn, fall back to display |
| Write fails | Report error, fall back to display |
| Download fails | Report error, fall back to display |
| Google Drive unavailable | Warn, fall back to display |

**Fallback principle:** User never loses content due to write/download failure. Always fall back to display mode showing full content.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.4.0 | 2025-12-19 | Streamlined, added encoding reference |
| 0.3.0 | 2025-12-19 | Reset to pre-release versioning |
| 1.2 | 2025-12-19 | Added download mode |
