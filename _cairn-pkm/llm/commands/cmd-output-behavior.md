# Output Behavior Pattern
*Type: Include | Version: 1.0 | Updated: 2025-12-18*

## Overview

Shared pattern for file output operations. Commands reference this rather than duplicating logic.

---

## Configuration

Read from `_local/user-prefs.yaml`:

```yaml
file_operations: "display"    # display | write | confirm
write_target: "local"         # local | gdrive
gdrive_vault_path: ""         # Required if write_target is gdrive
```

---

## Pattern Definition

```
OUTPUT_FILE(filepath, content):

  READ: file_operations, write_target, gdrive_vault_path from {VAULT_PATH}/_local/user-prefs.yaml
  DEFAULTS: 
    file_operations = "display" if not found
    write_target = "local" if not found

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
      RETURN: displayed

    CASE "confirm":
      OUTPUT: "📄 PROPOSED FILE"
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Filename: {filename}"
      OUTPUT: "Path: {filepath}"
      OUTPUT: ""
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Write this file? (yes/no)"
      WAIT FOR: user_response
      
      IF user_response matches "yes" OR "y":
        CALL: WRITE_TO_TARGET(filepath, content)
        RETURN: written
      ELSE:
        OUTPUT: "File not written."
        RETURN: cancelled

    CASE "write":
      CALL: WRITE_TO_TARGET(filepath, content)
      RETURN: written


WRITE_TO_TARGET(filepath, content):

  # Convert absolute vault path to relative path
  relative_path = filepath with {VAULT_PATH}/ prefix removed

  SWITCH write_target:

    CASE "local":
      # Direct filesystem write
      CREATE: directory if not exists
      WRITE: content to filepath
      OUTPUT: "✓ Created {filepath}"

    CASE "gdrive":
      # Validate gdrive_vault_path is set
      IF gdrive_vault_path is empty:
        OUTPUT: "⚠️ gdrive_vault_path not configured in user-prefs.yaml"
        OUTPUT: "Falling back to display mode..."
        EXECUTE: display mode output
        RETURN: error
      
      # Construct Google Drive path
      gdrive_path = gdrive_vault_path + "/" + relative_path
      
      # Use Google Drive tool to create/update file
      CALL: google_drive_create_or_update(gdrive_path, content)
      OUTPUT: "✓ Created {gdrive_path} in Google Drive"
```

---

## Usage in Commands

Commands call this pattern instead of implementing their own output logic:

```
### Phase N: Output
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}

CALL: OUTPUT_FILE(filepath, content)
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| user-prefs.yaml missing | Use "display" mode, continue |
| file_operations not set | Use "display" mode, continue |
| write_target not set | Use "local" if file_operations requires write |
| Invalid file_operations value | Use "display" mode, warn user |
| Invalid write_target value | Use "local", warn user |
| gdrive_vault_path empty (gdrive mode) | Warn user, fall back to display |
| Local write fails | Report error, show content for manual copy |
| Google Drive write fails | Report error, show content for manual copy |
| Google Drive not connected | Warn user, fall back to display |

---

## Fallback Behavior

If write fails in "write" or "confirm" mode (for any write_target):

```
ON write_error:
  OUTPUT: "⚠️ Could not write to {target_path}: {error}"
  OUTPUT: "Falling back to display mode..."
  EXECUTE: display mode output
```

This ensures user never loses content due to a write failure.

### Google Drive Specific Fallbacks

```
IF write_target == "gdrive":

  IF Google Drive tool not available:
    OUTPUT: "⚠️ Google Drive access not available in this session"
    OUTPUT: "Falling back to display mode..."
    EXECUTE: display mode output

  IF gdrive_vault_path not configured:
    OUTPUT: "⚠️ gdrive_vault_path not set in user-prefs.yaml"
    OUTPUT: "Set this to your vault's location in Google Drive"
    OUTPUT: "Example: Obsidian/MyVault"
    OUTPUT: ""
    OUTPUT: "Falling back to display mode..."
    EXECUTE: display mode output
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-18 | Initial version |
| 1.1 | 2025-12-18 | Added write_target support (local, gdrive) |
