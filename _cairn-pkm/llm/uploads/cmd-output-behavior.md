# Output Behavior Pattern
*Type: Include | Version: 0.5.0 | Updated: 2025-12-21*

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

  # VBM: State operation before executing
  OUTPUT: "{operation} {filename} in {directory}"

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
      TRY:
        output_path = /mnt/user-data/outputs/{filename}
        WRITE: content to output_path
        CALL: present_files([output_path])
        OUTPUT: "Download the file above and save to: {filepath}"
      ON FAILURE:
        CALL: FALLBACK_WITH_VISIBILITY("download", error, filepath, content)

    CASE "confirm":
      OUTPUT: "📄 PROPOSED FILE"
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Write this file? (yes/no)"
      IF confirmed: 
        TRY:
          CALL WRITE_TO_TARGET(filepath, content)
        ON FAILURE:
          CALL: FALLBACK_WITH_VISIBILITY("write", error, filepath, content)
      ELSE: 
        OUTPUT "File not written."

    CASE "write":
      TRY:
        CALL: WRITE_TO_TARGET(filepath, content)
      ON FAILURE:
        CALL: FALLBACK_WITH_VISIBILITY("write", error, filepath, content)


WRITE_TO_TARGET(filepath, content):

  SWITCH write_target:

    CASE "local":
      CREATE: directory if not exists
      WRITE: content to filepath
      OUTPUT: "✓ Created {filepath}"

    CASE "gdrive":
      IF gdrive_vault_path empty: 
        THROW: "Google Drive path not configured"
      gdrive_path = gdrive_vault_path + "/" + relative_path
      CALL: google_drive_create_or_update(gdrive_path, content)
      OUTPUT: "✓ Created {gdrive_path} in Google Drive"
```

---

## Graceful Fallback Chain (GFC)

When operations fail, degrade gracefully with full visibility:

```
FALLBACK_WITH_VISIBILITY(failed_mode, error, filepath, content):

  # Report failure clearly
  OUTPUT: "⚠ {failed_mode} failed: {error}"
  
  # Show fallback path
  SWITCH failed_mode:
    CASE "write":
      OUTPUT: "↳ Falling back to download mode"
      TRY:
        output_path = /mnt/user-data/outputs/{filename}
        WRITE: content to output_path
        CALL: present_files([output_path])
        OUTPUT: "↳ Download the file above and save to: {filepath}"
        RETURN
      ON FAILURE:
        OUTPUT: "↳ Download also failed: {error}"
        OUTPUT: "↳ Falling back to display mode"
        # Fall through to display
        
    CASE "download":
      OUTPUT: "↳ Falling back to display mode"
      # Fall through to display

  # Final fallback: display (always works)
  OUTPUT: "↳ Content preserved below"
  OUTPUT: ""
  OUTPUT: "📄 FILE CONTENT"
  OUTPUT: "═══════════════════════════════════════"
  OUTPUT: "Filename: {filename}"
  OUTPUT: "Path: {filepath}"
  OUTPUT: ""
  OUTPUT: {content}
  OUTPUT: "═══════════════════════════════════════"
  OUTPUT: "Copy this content and save to the path above."
```

**Fallback chain order:** write → download → display

**Principle:** User never loses content. Display mode is the ultimate safety net.

---

## Usage in Commands

```
# VBM: Verify before modify
VERIFY: target conditions as expected
STATE: operation about to perform

# Execute with GFC protection
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}
CALL: OUTPUT_FILE(filepath, content)
```

---

## Error Handling Summary

| Situation | Response |
|-----------|----------|
| prefs file missing | Use display mode |
| Invalid file_operations | Use display mode, warn |
| Invalid write_target | Use local, warn |
| gdrive_vault_path empty | Warn, fall back per GFC |
| Write fails | Fall back per GFC (write → download → display) |
| Download fails | Fall back per GFC (download → display) |
| Google Drive unavailable | Warn, fall back per GFC |

**Core principle:** User never loses content due to write/download failure. Always fall back to display mode showing full content with clear messaging about what happened.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.5.0 | 2025-12-21 | Added GFC fallback chain with visibility messaging |
| 0.4.0 | 2025-12-19 | Streamlined, added encoding reference |
| 0.3.0 | 2025-12-19 | Reset to pre-release versioning |
| 1.2 | 2025-12-19 | Added download mode |
