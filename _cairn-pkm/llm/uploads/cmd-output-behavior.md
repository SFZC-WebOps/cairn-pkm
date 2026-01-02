# Output Behavior Pattern
*Type: Include | Updated: 2025-12-23*

Shared pattern for file output operations. Commands reference this rather than duplicating logic.

---

## Configuration

Read from `/mnt/project/cairn-pkm-user-prefs.yaml`:

```yaml
file_operations: "download"    # download | write
vault_location: "local"        # local | cloud
cloud_vault_path: ""           # Required if vault_location is cloud
```

---

## Mode Comparison

| Mode | How It Works | Best For |
|------|--------------|----------|
| `download` | Shows content + creates downloadable file | Web-based LLM OR local filesystem, works anywhere |
| `write` | Writes directly to filesystem | Desktop app with MCP/filesystem access |

---

## Pattern Definition

```
OUTPUT_FILE(filepath, content):

  # Apply encoding fix (see cmd-shared-patterns.md)
  content = ftfy.fix_text(content)

  # VBM: State operation before executing
  OUTPUT: "{operation} {filename} in {directory}"

  READ: file_operations from prefs
  DEFAULT: file_operations = "download"

  SWITCH file_operations:

    CASE "download":
      # Show content on screen
      OUTPUT: "📋 FILE CONTENT"
      OUTPUT: "═══════════════════════════════════════"
      OUTPUT: "Filename: {filename}"
      OUTPUT: "Path: {filepath}"
      OUTPUT: ""
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════"
      
      # Also create downloadable file
      TRY:
        output_path = /mnt/user-data/outputs/{filename}
        WRITE: content to output_path
        CALL: present_files([output_path])
        OUTPUT: ""
        OUTPUT: "💾 Download the file above and save to: {filepath}"
      ON FAILURE:
        # If download creation fails, content is still visible above
        OUTPUT: ""
        OUTPUT: "⚠️  Could not create download file: {error}"
        OUTPUT: "Copy the content above and save manually."

    CASE "write":
      TRY:
        CREATE: directory if not exists
        WRITE: content to filepath
        OUTPUT: "✓ Created {filepath}"
      ON FAILURE:
        CALL: FALLBACK_WITH_VISIBILITY("write", error, filepath, content)
```

---

## Graceful Fallback Chain (GFC)

When write operations fail, fall back with content visibility:

**Fallback chain order:** write → download

```
FALLBACK_WITH_VISIBILITY(failed_mode, error, filepath, content):

  # Report failure clearly
  OUTPUT: "⚠️  {failed_mode} failed: {error}"
  OUTPUT: "↳ Falling back to download mode"
  OUTPUT: ""
  
  # Show content + attempt download
  OUTPUT: "📋 FILE CONTENT"
  OUTPUT: "═══════════════════════════════════════"
  OUTPUT: "Filename: {filename}"
  OUTPUT: "Path: {filepath}"
  OUTPUT: ""
  OUTPUT: {content}
  OUTPUT: "═══════════════════════════════════════"
  
  TRY:
    output_path = /mnt/user-data/outputs/{filename}
    WRITE: content to output_path
    CALL: present_files([output_path])
    OUTPUT: ""
    OUTPUT: "💾 Download the file above and save to: {filepath}"
  ON FAILURE:
    # If even download fails, content is still visible
    OUTPUT: ""
    OUTPUT: "⚠️  Could not create download file: {error}"
    OUTPUT: "Copy the content above and save manually."
```

**Fallback chain order:** write → download (with visible content)

**Principle:** User never loses content. Download mode always shows content on screen even if file creation fails.

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
| prefs file missing | Use download mode |
| Invalid file_operations | Use download mode, warn |
| Write fails | Fall back to download mode |
| Download file creation fails | Show content anyway (copy/paste) |

**Core principle:** User never loses content. Download mode always displays content on screen, with downloadable file as convenience when possible.
