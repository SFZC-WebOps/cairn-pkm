# INSTALLATION.md Addition: User Preferences

Add this section after "### 3. Install Required Plugins" and before "### 4. Explore Example Content":

---

### 4. Configure User Preferences

Edit `_local/user-prefs.yaml` with your settings:

```yaml
# Your name for task assignments
default_assignee: "Your Name"

# Your timezone (IANA format)
timezone: "America/New_York"

# How LLM commands output files (see below)
file_operations: "display"
write_target: "local"
gdrive_vault_path: ""
```

**Choose your output mode:**

| Your Setup | Settings |
|------------|----------|
| Copy/paste manually (safest) | `file_operations: "display"` |
| Desktop app or MCP with filesystem | `file_operations: "write"`, `write_target: "local"` |
| Web-based LLM with Google Drive connected | `file_operations: "write"`, `write_target: "gdrive"`, `gdrive_vault_path: "Obsidian/YourVault"` |
| Review before writing | `file_operations: "confirm"`, then set `write_target` |

If unsure, leave as `display` — you can always change it later.

---

Also update the existing section numbers:
- "### 4. Explore Example Content" → "### 5. Explore Example Content"
- "### 5. Start Using It" → "### 6. Start Using It"

---
