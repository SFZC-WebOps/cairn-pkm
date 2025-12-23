# Start Here: Cairn-PKM Setup

Welcome! This guide gets you from download to working system in about 10 minutes.

*Version: 0.5.0*

---

## What You Downloaded

Cairn-PKM is a personal knowledge management system that works with:
- **Obsidian** — Your notes live here as plain markdown files
- **An LLM** (Claude, etc.) — Helps you work with your notes via conversational commands

The system has two parts:
1. **Vault files** — The folders and templates that go in Obsidian
2. **LLM files** — Command specs that teach the LLM how to help you

---

## Step 1: Set Up Obsidian Vault

### 1.1 Open in Obsidian

1. Open Obsidian
2. Click "Open folder as vault"
3. Select the folder you downloaded/unzipped
4. Trust the author when prompted (for community plugins)

### 1.2 Install Required Plugins

Go to **Settings → Community Plugins → Browse** and install:

| Plugin | Required | Settings to Configure |
|--------|----------|----------------------|
| **Dataview** | Yes | Enable "JavaScript Queries" |
| **Templater** | Yes | Template folder: `_cairn-pkm/templates/` |
| **Tasks** | Recommended | Default settings work |

**Quick check:** After installing, restart Obsidian.

---

## Step 2: Set Up LLM Project

The LLM needs access to command specifications to understand `!` commands.

### For Claude (claude.ai)

1. Go to **claude.ai → Projects** (left sidebar)
2. Click **"Create Project"**
3. Name it (e.g., "Cairn-PKM" or "My PKM")

### Upload Files to Project

In your new project, click **"Add content" → "Upload files"**

Upload **all files** from `_cairn-pkm/llm/uploads/` in your vault.

This folder contains everything the LLM needs — architecture docs, command specs, and preferences template. Select all and upload together.

### Add Project Instructions

In **Project Settings → Instructions**, copy the content from:

`_cairn-pkm/llm/llm-project-instructions.md`

(This file contains the instructions to paste — it's not uploaded as a project file.)

### For Other LLMs

The same files work with other LLMs that support project/context features:
- Upload the same files to your LLM's project or context system
- Adapt the instructions format as needed for your platform

---

## Step 3: Configure Your Preferences

Edit `cairn-pkm-user-prefs.yaml` before uploading (or upload and reconfigure via `!setup`):

```yaml
# Your name for task assignments
default_assignee: "Your Name"

# Your timezone (IANA format)
timezone: "America/Los_Angeles"

# How LLM outputs files — start with "display" if unsure
file_operations: "display"

# Where files go when using write mode
write_target: "local"

# Google Drive path (only if using gdrive)
gdrive_vault_path: ""
```

### File Operations Modes

| Mode | What Happens | Best For |
|------|--------------|----------|
| `display` | Shows content to copy/paste | Getting started, maximum control |
| `download` | Creates downloadable file | Web-based Claude without filesystem access |
| `write` | Writes directly to vault | Desktop app with MCP, or Google Drive connected |
| `confirm` | Shows content, asks, then writes | Write access but want to review first |

**Recommendation:** Start with `display` until you're comfortable, then upgrade to `write` if you have filesystem/Drive access.

---

## Step 4: Run Guided Setup

Now you're ready! Start a conversation in your LLM project and type:

```
!setup
```

This will:
- Verify your configuration
- Check vault access (if using write mode)
- Confirm folder structure
- Optionally create starter content
- Validate everything works

---

## Step 5: Start Using Cairn-PKM

After setup completes, try these commands:

| Command | What It Does |
|---------|--------------|
| `!tour` | Guided walkthrough (recommended first!) |
| `!hi` | Open interactive session menu |
| `!create area` | Create your first area (e.g., area-personal) |
| `!create task` | Create a task |
| `!bye` | Close session with logging |

---

## Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| Commands don't work | Are you in the LLM project? (not a standalone chat) |
| "File not found" errors | Check all cmd-*.md files are uploaded |
| Dataview shows code blocks | Settings → Dataview → Enable "JavaScript Queries" |
| Templates not expanding | Settings → Templater → Template folder = `_cairn-pkm/templates/` |
| Can't write to vault | Check file_operations mode, verify access with `!setup check` |

---

## What's Next?

- **Explore:** Look at example content in `Tracks/`, `Objects/`, `Capture/`
- **Learn:** Read `_ARCHITECTURE.md` for the full system overview
- **Customize:** Add your own templates to `_local/templates/`
- **Grow:** Start with one area, add structure as you need it

---

## Getting Help

- **Re-run setup:** `!setup` or `!setup check` anytime
- **Documentation:** `_ARCHITECTURE.md`, `_INSTALLATION.md`
- **Command help:** Ask the LLM about any `!` command

---

*Cairn-PKM v0.5.0 — Plain markdown, portable, AI-friendly*
