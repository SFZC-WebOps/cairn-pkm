# Start Here: Cairn-PKM Setup

Welcome! This guide gets you from download to working system in about 10 minutes.

*Version: 0.5.0*

---

## What You Downloaded

Cairn-PKM is a personal knowledge management system that works with:
- **Obsidian** — Your notes live here as plain markdown files
- **An LLM** — Helps you work with your notes via conversational commands

The system has two parts:
1. **Vault files** — The folders and templates that go in Obsidian
2. **LLM command files** — Specs that teach the LLM how to help you

---

## Choose Your Setup Path

### Path A: Desktop App with Filesystem Access

**Use this if:** You run an LLM desktop app with filesystem access (e.g., MCP server)

- Vault lives locally on your machine
- LLM can read and write files directly
- Both `display` and `write` modes work

→ Continue to [Step 1: Obsidian Setup](#step-1-set-up-obsidian-vault)

### Path B: Web UI with Cloud Sync

**Use this if:** You use a web-based LLM (claude.ai, ChatGPT, etc.)

- Entire vault syncs to a cloud service (Google Drive, etc.)
- LLM connects to cloud via integration (read-only)
- `display` mode only — you place files in your synced vault manually

**Requirements:**
- Cloud service that your LLM can connect to
- Obsidian vault synced to that cloud service
- Cloud connector enabled in your LLM

→ Continue to [Step 1: Obsidian Setup](#step-1-set-up-obsidian-vault)

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

### 1.3 For Path B Only: Sync Vault to Cloud

If using Path B (Web UI), your entire vault needs to sync to a cloud service your LLM can connect to:

1. Move or set up your vault folder inside your cloud-synced location
   - Example: `Google Drive/Obsidian/Cairn-PKM/`
2. Verify sync is working (changes appear in cloud)
3. Enable cloud connector in your LLM interface

---

## Step 2: Set Up LLM Project

The LLM needs command specifications in its project/memory to understand `!` commands.

### 2.1 Create a Project

1. Look for **Projects**, **Custom Instructions**, or **Context** in your LLM interface
2. Create a new project (e.g., "Cairn-PKM" or "My PKM")

### 2.2 Upload Command Files

Upload **all files** from `_cairn-pkm/llm/uploads/` to your LLM project.

This folder contains architecture docs, command specs, and preferences template. The LLM needs these in its project memory to act on commands.

### 2.3 Add Project Instructions

Copy the content from `_cairn-pkm/llm/llm-project-instructions.md` into your project's instructions or system prompt area.

### 2.4 For Path B Only: Connect Cloud Service

In your LLM interface, connect the cloud service where your vault syncs. This lets the LLM read your tracks, tasks, and objects.

---

## Step 3: Configure Your Preferences

Edit `cairn-pkm-user-prefs.yaml` in your LLM project (or reconfigure via `!setup`):

```yaml
# Your name for task assignments
default_assignee: "Your Name"

# Your timezone (IANA format)
timezone: "America/Los_Angeles"

# How LLM outputs files
file_operations: "display"    # display | write
```

### File Operations Modes

| Mode | What Happens | Works With |
|------|--------------|------------|
| `display` | Shows content + presents downloadable file | Both paths |
| `write` | Writes directly to filesystem | Path A only |

**Recommendation:** Start with `display`. It works everywhere and gives you both viewable content and a downloadable file.

---

## Step 4: Run Guided Setup

Start a conversation in your LLM project and type:

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
| "File not found" errors | Check all files from `uploads/` are uploaded to project |
| LLM can't see vault files | Path B: Check cloud connector is enabled |
| Can't write to vault | Path A only — verify filesystem access; Path B must use display mode |
| Dataview shows code blocks | Settings → Dataview → Enable "JavaScript Queries" |
| Templates not expanding | Settings → Templater → Template folder = `_cairn-pkm/templates/` |

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
