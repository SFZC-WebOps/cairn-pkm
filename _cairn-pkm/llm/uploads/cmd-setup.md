# Initial Setup
*Type: Interactive | Updated: 2025-01-01*

## Overview

Get a new Cairn-PKM vault configured. The system checks what's in place and helps set up what's missing — conversationally.

**Shortcut:** `!setup` to run setup, `!setup check` to verify configuration

---

## First-Time Setup

When vault is new or unconfigured:

```
LLM: Welcome to Cairn-PKM! Let me help you get set up.
     
     I'll check a few things:
     - Obsidian plugins (Dataview, Templater, Tasks)
     - Your preferences (timezone, file handling)
     - Vault structure (folders in place)
     
     First — are the required plugins installed?
     
     **Required:**
     - Dataview (with JavaScript queries enabled)
     - Templater (template folder set to _cairn-pkm/templates/)
     
     **Recommended:**
     - Tasks (for better checkbox handling)
```

### Plugin Check

```
User: I have Dataview and Templater

LLM: Great. Just confirm these settings:
     
     **Dataview:**
     Settings → Enable "JavaScript Queries"
     
     **Templater:**  
     Settings → Template folder location → `_cairn-pkm/templates/`
     
     Those good?

User: Yes

LLM: Perfect. Now let's set your preferences.
```

### Preferences

```
LLM: A few preferences to configure:
     
     **Your name** (for task assignments)
     What name should appear on tasks you create?

User: Greg

LLM: Got it. 
     
     **Timezone** (for timestamps)
     What's your timezone? Examples: America/Los_Angeles, America/New_York, Europe/London

User: Los Angeles

LLM: America/Los_Angeles — confirmed.
     
     **File handling**
     When I create files, should I:
     - Write directly to your vault (needs filesystem access)
     - Show content and provide a download
     
     If you're not sure, "download" works everywhere.

User: Download for now

LLM: OK. Here's your configuration:
     
     ```yaml
     default_assignee: "Greg"
     timezone: "America/Los_Angeles"
     file_operations: "download"
     ```
     
     Save this as `cairn-pkm-user-prefs.yaml` in your LLM project files.
```

### Structure Check

```
LLM: Checking vault structure...
     
     ✓ _cairn-pkm/ exists
     ✓ Capture/ exists
     ✓ Objects/ exists
     ✓ Tracks/ exists
     ✗ _local/ missing
     
     I need to create _local/ with its subfolders (data, templates, views).
     This is where your customizations go.
     
     Create it?

User: Yes

LLM: ✓ Created _local/data/
     ✓ Created _local/templates/
     ✓ Created _local/views/
```

### Starter Content

```
LLM: Your vault is empty. Want me to create a starter area?
     
     This gives you something to explore and a place to put things
     until you set up your own structure.
     
     I'd suggest area-personal — a general catch-all.

User: Sure

LLM: ✓ Created Tracks/area-personal/
     
     Setup complete! Your vault is ready.
     
     **Quick start:**
     - "Show me area-personal" to see your new area
     - "Create a task" to add something to do
     - "I want to track [project]" to start a project
     
     Or just tell me what you're working on.
```

---

## Shortcut: !setup

Runs the setup flow:

```
!setup
```

If already configured, asks if you want to update anything.

---

## Shortcut: !setup check

Quick verification without prompts:

```
!setup check
```

**Output:**
```
LLM: Configuration check:
     
     **Preferences:** ✓ Found (Greg, America/Los_Angeles, download)
     **Structure:** ✓ All folders present
     **Tracks:** 1 area, 0 projects
     
     Everything looks good.
```

---

## What Gets Checked

| Check | What It Verifies |
|-------|------------------|
| Preferences | cairn-pkm-user-prefs.yaml accessible |
| Structure | _cairn-pkm/, _local/, Capture/, Objects/, Tracks/ exist |
| Plugins | (Manual check — user confirms) |

---

## Preferences File

Generated during setup:

```yaml
# Cairn-PKM User Preferences

default_assignee: "{name}"
timezone: "{timezone}"
file_operations: "{download|write}"
```

**Location:** Upload to LLM project files

---

## Troubleshooting

### Dataview queries show as code

```
LLM: Dataview isn't processing queries. Check:
     Settings → Dataview → Enable "JavaScript Queries"
     
     Then refresh the note (Cmd/Ctrl + R).
```

### Templater not working

```
LLM: Templates aren't expanding. Check:
     Settings → Templater → Template folder location
     Should be: _cairn-pkm/templates/
```

### Can't access filesystem

```
LLM: I don't have filesystem access. That's OK — 
     I'll show content and provide downloads instead.
     
     If you want direct file writing later, set up
     filesystem access in your LLM tool.
```

---

## Re-Running Setup

Setup is idempotent — safe to run again:

```
LLM: I see you're already set up:
     - Preferences: Greg, America/Los_Angeles, download
     - Structure: Complete
     - Tracks: 2 areas, 3 projects
     
     Want to update anything?
```
