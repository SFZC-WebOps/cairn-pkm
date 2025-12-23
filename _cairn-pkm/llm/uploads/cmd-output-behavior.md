# Output Behavior Pattern
*Type: Include | Updated: 2025-12-22*

Shared pattern for file output operations. Commands reference this rather than duplicating logic.

---

## Configuration

Read from project files `cairn-pkm-user-prefs.yaml`:

```yaml
file_operations: "display"    # display | write
```

---

## Mode Comparison

| Mode | How It Works | Best For |
|------|--------------|----------|
| `display` | Shows content + presents downloadable file | Universal — works everywhere |
| `write` | Writes directly to filesystem | Desktop app with filesystem access |

---

## Pattern Definition

```
OUTPUT_FILE(filepath, content):

  # Apply encoding fix (see cmd-shared-patterns.md)
  content = ftfy.fix_text(content)

  # VBM: State operation before executing
  OUTPUT: "{operation} {filename} in {directory}"

  READ: file_operations from prefs
  DEFAULT: file_operations = "display"

  SWITCH file_operations:

    CASE "display":
      OUTPUT: "📋 FILE CONTENT"
      OUTPUT: "═══════════════════════════════════════════"
      OUTPUT: "Filename: {filename}"
      OUTPUT: "Path: {filepath}"
      OUTPUT: ""
      OUTPUT: {content}
      OUTPUT: "═══════════════════════════════════════════"
      
      TRY:
        output_path = /mnt/user-data/outputs/{filename}
        WRITE: content to output_path
        CALL: present_files([output_path])
        OUTPUT: "Download above or copy content to: {filepath}"
      ON FAILURE:
        OUTPUT: "Copy this content and save to the path above."

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

When write operations fail, degrade gracefully with full visibility:

```
FALLBACK_WITH_VISIBILITY(failed_mode, error, filepath, content):

  # Report failure clearly
  OUTPUT: "⚠️ {failed_mode} failed: {error}"
  OUTPUT: "↳ Falling back to display mode"
  
  # Final fallback: display (always works)
  OUTPUT: "↳ Content preserved below"
  OUTPUT: ""
  OUTPUT: "📋 FILE CONTENT"
  OUTPUT: "═══════════════════════════════════════════"
  OUTPUT: "Filename: {filename}"
  OUTPUT: "Path: {filepath}"
  OUTPUT: ""
  OUTPUT: {content}
  OUTPUT: "═══════════════════════════════════════════"
  
  TRY:
    output_path = /mnt/user-data/outputs/{filename}
    WRITE: content to output_path
    CALL: present_files([output_path])
    OUTPUT: "Download above or copy content to: {filepath}"
  ON FAILURE:
    OUTPUT: "Copy this content and save to the path above."
```

**Fallback chain:** write → display

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
| Write fails | Fall back to display with download |
| Download fails | Show content for copy/paste |

**Core principle:** User never loses content due to write failure. Always fall back to display mode showing full content with clear messaging about what happened.
