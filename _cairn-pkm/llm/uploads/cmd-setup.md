# !setup - Guided Setup
*Type: Interactive | Updated: 2025-12-21*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!setup` | Run guided setup (first-time or verification) |
| `!setup check` | Verify current configuration without changes |

**Workflow:** `!setup` → LLM project check → Plugins → Preferences → Vault access → Structure → Starter content → Validation

**Prerequisite:** LLM project must be set up first — see START-HERE.md in your vault.

**Idempotent:** Safe to re-run. Detects existing configuration and offers to keep or update.

---

## Initialization

Per `cmd-shared-patterns.md`

**First-run detection:**
```
CHECK: Project files accessible (cairn-pkm-user-prefs.yaml exists)
CHECK: Vault structure present (Tracks/, Objects/, Capture/, _local/)

IF prefs missing OR structure incomplete:
  MODE: "first_run"
  OUTPUT: "Welcome to Cairn-PKM! Let's get you set up."
ELSE:
  MODE: "verification"
  OUTPUT: "Cairn-PKM detected. Running configuration check..."
```

---

## Execution

### Phase 1: Welcome & Mode Detection

```
DETECT: first_run | verification

IF first_run:
  OUTPUT:
  ═══════════════════════════════════════════════
  🏔️ Welcome to Cairn-PKM Setup
  ═══════════════════════════════════════════════
  
  I'll walk you through configuring your vault.
  This takes about 5-10 minutes.
  
  You can re-run !setup anytime to verify or update settings.
  
  Ready to begin? (yes to continue)
  
  Tip: During setup, you can type `skip`, `back`, or `status` anytime.

IF verification:
  OUTPUT:
  ═══════════════════════════════════════════════
  🏔️ Cairn-PKM Configuration Check
  ═══════════════════════════════════════════════
  
  Checking your current setup...
  
  [Proceed to Phase 2 automatically]
```

### Phase 2: LLM Project Verification

```
OUTPUT:
───────────────────────────────────────────────
🤖 Step 1/7: LLM Project Check
───────────────────────────────────────────────

Verifying I can access project files...

ATTEMPT: List project files

IF files visible:
  OUTPUT:
  ✓ Project files accessible
  
  Found: {count} files
  {list key files: _ARCHITECTURE.md, cmd-*.md, etc.}
  
  CONTINUE to Phase 3

IF files not visible OR key files missing:
  OUTPUT:
  ⚠️ Can't access required project files.
  
  This usually means:
  - You're in a standalone conversation (not the project)
  - Files weren't uploaded to the project
  - Some command files are missing
  
  **To fix:**
  1. See START-HERE.md in your vault for setup instructions
  2. Upload all files from _cairn-pkm/llm/ to your LLM project
  3. Start a new conversation within the project
  4. Run !setup again
  
  Continue anyway? (yes / no)
  
  IF "no": STOP with guidance to read START-HERE.md
  IF "yes": CONTINUE with warning

RECORD: llm_project_verified = true
CONTINUE to Phase 3
```

### Phase 3: Plugin Verification

```
OUTPUT:
───────────────────────────────────────────────
📦 Step 2/7: Plugin Check
───────────────────────────────────────────────

Required plugins for Cairn-PKM:

1. **Dataview** — Powers task queries and dashboards
   - Must enable: JavaScript Queries (Settings → Dataview)
   
2. **Templater** — Template expansion for file creation
   - Set template folder: _cairn-pkm/templates/
   - Optional second folder: _local/templates/

3. **Tasks** (Recommended) — Enhanced task management
   - Provides checkbox features and task queries

Can you confirm these plugins are installed and configured?
(yes / no / help)

IF "no" or "help":
  OUTPUT:
  To install plugins:
  1. Settings → Community Plugins → Browse
  2. Search for plugin name → Install → Enable
  3. Configure settings as noted above
  
  Let me know when ready to continue.

IF "yes":
  RECORD: plugins_verified = true
  
  OUTPUT:
  ✓ Plugins confirmed
  
  **Troubleshooting notes for later:**
  - Dataview queries show as code blocks → Settings → Dataview → Enable "JavaScript Queries"
  - Templates not expanding → Settings → Templater → Template folder = `_cairn-pkm/templates/`
  - Task checkboxes not working → Verify Tasks plugin is enabled
  
  (Save these notes — common issues if something isn't working)
  
  CONTINUE to Phase 4
```

### Phase 4: User Preferences

```
OUTPUT:
───────────────────────────────────────────────
⚙️ Step 3/7: User Preferences
───────────────────────────────────────────────

IF existing prefs found:
  Current settings:
  - Default assignee: {current_value or "(not set)"}
  - Timezone: {current_value}
  - File operations: {current_value}
  - Write target: {current_value}
  
  Keep these settings? (yes / update)

IF first_run OR user says "update":
  PROMPT each setting:

  1. **Default Assignee**
     Your name for task assignments (or leave blank):
     Example: "Alex Chen" or "webops"
     > 

  2. **Timezone**
     Your IANA timezone for timestamps:
     Examples: America/New_York, America/Los_Angeles, Europe/London
     Default: America/Los_Angeles
     > 

  3. **File Operations Mode**
     How should I output files?
     
     - display  → Show content to copy/paste (safest, works everywhere)
     - download → Create downloadable file (web interface)
     - write    → Write directly to filesystem (needs filesystem or Drive access)
     - confirm  → Show content, ask, then write
     
     Recommended for most users: display
     > 

  4. **Write Target** (only if write/confirm selected)
     Where should files be written?
     
     - local  → Direct filesystem (needs filesystem access)
     - gdrive → Google Drive (needs Drive tool connected)
     > 

  5. **Google Drive Path** (only if gdrive selected)
     Path to your vault in Google Drive:
     Example: Obsidian/MyVault
     > 

GENERATE: cairn-pkm-user-prefs.yaml content
DISPLAY: Generated configuration for review

OUTPUT:
📄 Your preferences:

```yaml
default_assignee: "{value}"
timezone: "{value}"
file_operations: "{value}"
write_target: "{value}"
gdrive_vault_path: "{value}"
```

Save this configuration? (yes / edit)

ON "yes":
  RECORD: prefs_configured = true
  
  IF file_operations allows writing:
    ATTEMPT: Write to _local/cairn-pkm-user-prefs.yaml
  ELSE:
    OUTPUT: Save this file as: _local/cairn-pkm-user-prefs.yaml
    OUTPUT: Also upload to your LLM project.
```

### Phase 5: Vault Access Verification

```
OUTPUT:
───────────────────────────────────────────────
🔌 Step 4/7: Vault Access Check
───────────────────────────────────────────────

IF write_target == "local":
  OUTPUT:
  Testing local filesystem access...
  
  ATTEMPT: List contents of vault root path
  
  IF successful:
    OUTPUT:
    ✓ Filesystem access confirmed
    
    Detected vault contents:
    {list top-level folders found}
    
    Does this look like your Cairn-PKM vault? (yes / no / path issue)
    
    IF "no" or "path issue":
      OUTPUT:
      The path I'm accessing doesn't seem right.
      
      What's the correct path to your vault?
      Example: /Users/yourname/Documents/Obsidian/MyVault
      > 
      
      RETRY access check with new path
      
  IF failed:
    OUTPUT:
    ✗ Cannot access local filesystem
    
    This usually means:
    - Filesystem tools not connected or enabled
    - Path permissions issue
    
    Options:
    1. Fix access and retry
    2. Switch to 'download' mode (I'll create files for you to download)
    3. Switch to 'display' mode (I'll show content to copy/paste)
    4. Switch to 'gdrive' mode (if you have Google Drive connected)
    
    > 
    
    ON selection: Update file_operations/write_target accordingly

IF write_target == "gdrive":
  OUTPUT:
  Testing Google Drive access...
  
  ATTEMPT: google_drive_search for files in gdrive_vault_path
  
  IF successful AND files found:
    OUTPUT:
    ✓ Google Drive access confirmed
    
    Found vault at: {gdrive_vault_path}
    Detected contents:
    {list folders/files found}
    
    Does this look correct? (yes / no / path issue)
    
    IF "no" or "path issue":
      OUTPUT:
      Let's find the right path.
      
      I'll search for Cairn-PKM or Obsidian folders in your Drive...
      
      ATTEMPT: google_drive_search for "_cairn-pkm" or "Obsidian"
      
      IF found:
        OUTPUT:
        Found these potential vault locations:
        {list paths}
        
        Which one is your vault? (enter number or provide path)
        > 
      ELSE:
        OUTPUT:
        Couldn't find vault automatically.
        What's the exact path in Google Drive?
        Example: Obsidian/Cairn-PKM-dev
        > 
      
      UPDATE: gdrive_vault_path with correct value
      
  IF failed (no Drive access):
    OUTPUT:
    ✗ Cannot access Google Drive
    
    This usually means:
    - Google Drive tool not connected
    - Drive permissions not granted
    
    To connect Google Drive:
    1. Look for tools or integrations in your LLM interface
    2. Connect Google Drive
    3. Grant read/write permissions
    
    Options:
    1. Connect Drive and retry
    2. Switch to 'download' mode
    3. Switch to 'display' mode
    4. Switch to 'local' mode (if you have filesystem access)
    
    > 

IF file_operations in ["display", "download"]:
  OUTPUT:
  ℹ️ Access check not needed for {file_operations} mode
  
  Note: In this mode, I'll output files for you to place in your vault manually.
  You can switch to 'write' mode later if you set up filesystem or Drive access.

RECORD: access_verified = true
```

### Phase 6: Vault Structure

```
OUTPUT:
───────────────────────────────────────────────
📁 Step 5/7: Vault Structure
───────────────────────────────────────────────

CHECK each required directory:
  - _cairn-pkm/        (portable system)
  - _local/            (your customizations)
  - _local/data/       (local data storage)
  - _local/templates/  (custom templates)
  - _local/views/      (custom dashboards)
  - Capture/           (inbox)
  - Objects/           (cross-cutting entities)
  - Tracks/            (areas and projects)

FOR EACH directory:
  IF exists: OUTPUT "✓ {path}"
  IF missing: OUTPUT "✗ {path} — needs creation"

IF any missing:
  OUTPUT:
  Create missing directories? (yes / no / manual)
  
  ON "yes":
    CREATE: missing directories
    OUTPUT: "✓ Created {path}" for each
    
  ON "manual":
    OUTPUT: Commands to create manually:
    ```bash
    mkdir -p _local/{data,templates,views}
    mkdir -p {Capture,Objects,Tracks}
    ```

RECORD: structure_verified = true
```

### Phase 7: First Content (Optional)

```
OUTPUT:
───────────────────────────────────────────────
🌱 Step 6/7: Starter Content (Optional)
───────────────────────────────────────────────

IF Tracks/ is empty:
  OUTPUT:
  Your Tracks folder is empty. Would you like to create a starter area?
  
  Suggested: area-personal (general personal organization)
  
  Options:
  1. Create area-personal
  2. Create different area (I'll ask for name)
  3. Skip — I'll create my own later
  
  > 

  ON "1":
    CREATE: area-personal with standard structure
    OUTPUT: "✓ Created Tracks/area-personal/"
    
  ON "2":
    PROMPT: "Area name? (becomes area-{name})"
    CREATE: area-{name} with standard structure
    OUTPUT: "✓ Created Tracks/area-{name}/"
    
  ON "3":
    OUTPUT: "No problem. Use !create area when ready."

ELSE:
  OUTPUT:
  ✓ Tracks folder has content — skipping starter creation.
  
  Existing tracks:
  {list of area-* and p###-* folders}

RECORD: content_step_complete = true
```

### Phase 8: Validation & Summary

```
OUTPUT:
───────────────────────────────────────────────
✅ Step 7/7: Validation
───────────────────────────────────────────────

Running final checks...

VALIDATE:
  - [ ] Preferences file exists and is valid YAML
  - [ ] Required directories present
  - [ ] At least one track exists (or user skipped)
  - [ ] No obvious configuration conflicts

FOR EACH check:
  IF passes: OUTPUT "✓ {check description}"
  IF fails: OUTPUT "✗ {check description} — {remediation}"

───────────────────────────────────────────────

IF all checks pass:
  OUTPUT:
  ═══════════════════════════════════════════════
  🎉 Setup Complete!
  ═══════════════════════════════════════════════
  
  Your Cairn-PKM vault is ready to use.
  
  **Quick Start:**
  - `!tour` — Guided walkthrough (recommended for new users)
  - `!hi` — Open work session menu
  - `!create task` — Create your first task
  - `!create project` — Start a new project
  
  **Getting Help:**
  - Review _ARCHITECTURE.md for system overview
  - Check _INSTALLATION.md for detailed configuration
  - See START-HERE.md for LLM project setup reference
  - Re-run `!setup check` anytime to verify configuration
  
  ═══════════════════════════════════════════════

ELSE:
  OUTPUT:
  ═══════════════════════════════════════════════
  ⚠️ Setup Incomplete
  ═══════════════════════════════════════════════
  
  Some items need attention:
  {list of failed checks with remediation steps}
  
  Fix these issues and run `!setup check` to verify.
  
  ═══════════════════════════════════════════════
```

---

## !setup check — Verification Mode

Quick verification without interactive prompts:

```
OUTPUT:
🔍 Cairn-PKM Configuration Check
═══════════════════════════════════════════════

**LLM Project:**
✓/✗ Project files accessible ({count} files)

**Preferences:**
{display current prefs or "Not configured"}

**Vault Access:**
✓/✗ {write_target} access verified

**Structure:**
✓/✗ for each required directory

**Tracks:**
{count} areas, {count} projects

**Status:** {Ready | Needs attention}

═══════════════════════════════════════════════

IF issues found:
  Run `!setup` to fix interactively.
  For LLM project issues, see START-HERE.md
```

---

## Preferences File Template

Generated during setup:

```yaml
# Cairn-PKM User Preferences
# Generated: {YYYY-MM-DD}

# Your name for task assignments
default_assignee: "{user_input}"

# IANA timezone for timestamps
timezone: "{user_input}"

# How LLM commands output files
# Options: display | download | write | confirm
file_operations: "{user_input}"

# Where files are written (when file_operations is write/confirm)
# Options: local | gdrive
write_target: "{user_input}"

# Vault path in Google Drive (required if write_target is gdrive)
gdrive_vault_path: "{user_input}"
```

---

## Section Skip Commands

During setup, user can jump between sections:

| Command | Effect |
|---------|--------|
| `skip` | Skip current section |
| `back` | Return to previous section |
| `restart` | Start setup from beginning |
| `status` | Show which sections complete |
| `quit` | Exit setup (can resume later) |

---

## Re-running Setup

Setup is idempotent:

```
ON re-run:
  DETECT: existing configuration
  FOR EACH section:
    SHOW: current values
    ASK: "Keep or update?"
    IF "keep": skip to next section
    IF "update": run section prompts
```

This allows users to:
- Verify their configuration anytime
- Update individual settings without full re-setup
- Fix issues detected by `!setup check`

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Project files not accessible | Point to START-HERE.md for LLM project setup |
| Prefs file invalid YAML | "Preferences file has syntax errors. Would you like to recreate it?" |
| Can't create directories | "Unable to create directories. Check permissions or create manually." |
| User quits mid-setup | "Setup paused. Run !setup to continue where you left off." |
| Conflicting settings | "You selected gdrive but gdrive_vault_path is empty. Please provide path." |

Common errors: See `cmd-shared-patterns.md`

---

## Troubleshooting Reference

Quick fixes for common post-setup issues:

### LLM Project Issues

| Symptom | Fix |
|---------|-----|
| Commands not recognized | Are you in the LLM project? (not a standalone chat) |
| "File not found" for commands | Upload all cmd-*.md files to project |
| Setup can't see project files | Start new conversation within the project |
| Commands partially work | Some cmd-*.md files may be missing — re-upload all |

**First-time setup:** See START-HERE.md in your vault for complete LLM project setup instructions.

### Dataview Issues

| Symptom | Fix |
|---------|-----|
| Queries show as code blocks | Settings → Dataview → Enable "JavaScript Queries" |
| Queries show as code blocks | Settings → Dataview → Enable "Enable Inline Queries" |
| "Dataview is not available" | Ensure Dataview plugin is installed and enabled |
| Queries not updating | Refresh note (Ctrl/Cmd + R) or restart Obsidian |

### Templater Issues

| Symptom | Fix |
|---------|-----|
| Templates not expanding | Settings → Templater → Template folder location = `_cairn-pkm/templates/` |
| "Template not found" | Check template exists in configured folder |
| Custom templates not found | Add `_local/templates/` as additional template folder |
| Templater commands not working | Ensure Templater plugin is enabled, restart Obsidian |

### Tasks Plugin Issues

| Symptom | Fix |
|---------|-----|
| Checkboxes not interactive | Verify Tasks plugin is installed and enabled |
| Task queries not rendering | Tasks plugin may need Dataview — check both are enabled |

### File Operations Issues

| Symptom | Fix |
|---------|-----|
| "Write failed" errors | Check file_operations mode matches your environment |
| Google Drive writes failing | Verify gdrive_vault_path is set and Drive tool connected |
| Files not appearing in vault | If using gdrive, wait for sync; check gdrive_vault_path matches actual path |

### Vault Access Issues

| Symptom | Fix |
|---------|-----|
| "Cannot access local filesystem" | Filesystem tools not connected or enabled |
| "Cannot access Google Drive" | Drive tool not connected — check your LLM's integrations/tools |
| Wrong vault contents shown | Path is incorrect — provide full path to vault root |
| Can list files but can't write | Permission issue — check folder permissions or try different mode |
| gdrive path "not found" | Path is case-sensitive — verify exact folder names in Drive |

**Testing access manually:**
- Local: Ask the LLM to "list files in /path/to/your/vault"
- Drive: Ask the LLM to "search Google Drive for _cairn-pkm"

### Structure Issues

| Symptom | Fix |
|---------|-----|
| "Track not found" | Check Tracks/ folder exists and contains area-*/p###-*/ folders |
| "Home doc not found" | Verify _[track-name]-home.md exists in track folder |
| Commands can't find prefs | Upload cairn-pkm-user-prefs.yaml to LLM project |

Run `!setup check` to verify current configuration state.

---

## Integration

| Works With | How |
|------------|-----|
| !hi | Setup should complete before using !hi |
| All commands | Commands read prefs configured by !setup |
| _INSTALLATION.md | !setup automates manual steps in installation guide |
