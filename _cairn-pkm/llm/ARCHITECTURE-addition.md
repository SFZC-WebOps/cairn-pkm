# ARCHITECTURE.md Addition: User Preferences

Add this section after the `_local/ — Your Customizations` section:

---

## User Preferences

Cairn uses a simple preferences file for vault-specific settings that LLM commands need.

**Location:** `_local/user-prefs.yaml`

```yaml
# Identity
default_assignee: ""

# Time
timezone: "America/Los_Angeles"

# Output behavior
file_operations: "display"    # display | write | confirm
write_target: "local"         # local | gdrive
gdrive_vault_path: ""         # Required if write_target is gdrive
```

### Settings

| Setting | Purpose | Default |
|---------|---------|---------|
| `default_assignee` | Name used in task assignee field | (empty) |
| `timezone` | IANA timezone for timestamps | America/Los_Angeles |
| `file_operations` | How LLM outputs files | display |
| `write_target` | Where files are written | local |
| `gdrive_vault_path` | Vault path in Google Drive | (empty) |

### Output Behavior

The `file_operations` setting controls how LLM commands handle file creation:

| Mode | Behavior | Use When |
|------|----------|----------|
| `display` | Shows file content to copy/paste | No LLM file access, or prefer manual control |
| `write` | Writes files directly | LLM has access and you trust it |
| `confirm` | Shows content, waits for approval, then writes | LLM has access but you want to review |

### Write Targets

When `file_operations` is `write` or `confirm`, the `write_target` determines where files go:

| Target | How It Works | Setup Required |
|--------|--------------|----------------|
| `local` | Direct filesystem write | LLM needs MCP, desktop app, or computer use access |
| `gdrive` | Google Drive API | LLM with Drive tool connected; set `gdrive_vault_path` |

**Google Drive workflow:** LLM writes to Google Drive → Drive syncs to local → Obsidian sees the file.

### Extending Preferences

Commands use hardcoded defaults for most settings. If you need to override them, add the setting to your `user-prefs.yaml`:

```yaml
# Optional overrides (add only if needed)
default_priority: "high"          # Override task default
default_status: "scheduled"       # Override task default
references_folder: "Resources"    # Override !readme output location
```

See individual command specs for available overrides.

---
