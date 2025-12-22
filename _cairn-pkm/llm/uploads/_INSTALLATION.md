# Cairn-PKM Installation Guide

âš ï¸ **Pre-Release Software**: Cairn-PKM is in active development (version 0.5.0). Commands and workflows may change between versions.

Instructions for setting up a new Cairn-based Obsidian vault.

*Version: 0.5.0 | Last Updated: 2024-12-19*

---

## New Vault Setup

### 1. Download Cairn-PKM

1. Go to https://github.com/SFZC-WebOps/cairn-pkm/releases
2. Download the latest `cairn-vX.X.X.zip`
3. Unzip to your desired location
4. Rename the folder (optional): `Cairn-PKM-dev` â†’ `My-Vault`

### 2. Open in Obsidian

1. Open Obsidian
2. "Open folder as vault" â†’ Select your unzipped folder
3. Trust the author when prompted (for community plugins)

### 3. Install Required Plugins

**Required:**
1. **Dataview**
   - Settings â†’ Community Plugins â†’ Browse
   - Search "Dataview" â†’ Install â†’ Enable
   - Dataview Settings â†’ Enable "JavaScript Queries"

2. **Templater**
   - Install and enable via Community Plugins
   - Settings â†’ Templater â†’ Template folder location â†’ `_cairn-pkm/templates`
   - Optional: Add second folder `_local/templates` for custom templates

3. **Tasks** (Recommended)
   - Install and enable via Community Plugins
   - Provides enhanced task management features

**Recommended:**
- Calendar
- Periodic Notes
- QuickAdd
- Obsidian Git (for auto-backup of your vault)

### 4. Configure User Preferences

Edit `_local/user-prefs.yaml` with your settings:

```yaml
# Your name for task assignments
default_assignee: "Your Name"

# Your timezone (IANA format)
timezone: "America/New_York"

# How LLM commands output files (see below)
file_operations: "display"    # display | download | write | confirm
write_target: "local"         # local | gdrive
gdrive_vault_path: ""
```

**Choose your output mode:**

| Your Setup | Settings |
|------------|----------|
| Copy/paste manually (safest) | `file_operations: "display"` |
| Web-based LLM (Claude.ai) | `file_operations: "download"` |
| Desktop app or MCP with filesystem | `file_operations: "write"`, `write_target: "local"` |
| Web-based LLM with Google Drive connected | `file_operations: "write"`, `write_target: "gdrive"`, `gdrive_vault_path: "Obsidian/YourVault"` |
| Review before writing | `file_operations: "confirm"`, then set `write_target` |

If unsure, leave as `display` â€” you can always change it later.

### 5. Configure LLM Preferences (Optional)

If you use LLM commands (like Claude with the `!` commands), configure the LLM preferences file:

**In your vault:**
1. Copy the template to your local configuration:
   ```bash
   cp LLM/cairn-pkm-user-prefs.yaml _local/llm/cairn-pkm-user-prefs.yaml
   ```

2. Edit `_local/llm/cairn-pkm-user-prefs.yaml` with your settings (same as above)

**In your LLM project (Claude.ai, etc.):**
1. Upload `_local/llm/cairn-pkm-user-prefs.yaml` to your project files
2. The LLM will read settings from `/mnt/project/cairn-pkm-user-prefs.yaml`

**Why two files?**
- `_local/user-prefs.yaml` â€” Template/reference in your vault
- `_local/llm/cairn-pkm-user-prefs.yaml` â€” Your customized version for LLM uploads
- Keep both synchronized if you update settings

**Note:** LLM commands reference your vault structure but don't modify it directly unless you choose `file_operations: "write"`.

**Available Commands:**
Once configured, you can use conversational commands with your LLM:
- `!help` - Command reference and usage
- `!hi` / `!bye` - Session management with logging
- `!create` / `!edit` - Entity creation and editing (auto-detects types)
- `!capture` / `!skills` / `!changelog` - Capture and documentation

See `_cairn-pkm/llm/commands/` for complete command documentation.

### 6. Explore Example Content

- **Tracks/area-admin/** â€” Example area
- **Tracks/p001-office-move/** â€” Example project
- **Objects/** â€” Sample object files
- **Capture/** â€” Example quick notes

Review these to understand the structure, then delete and replace with your own content.

**Try the LLM commands:** If using with an LLM like Claude, try `!help` for available commands or `!hi` to open an interactive session menu.

### 7. Create Your First Area

```bash
# Create directory structure
mkdir -p Tracks/area-personal/resources
mkdir -p Tracks/area-personal/tasks
mkdir -p Tracks/area-personal/zzz

# Create home document
touch Tracks/area-personal/_area-personal-home.md
```

Or use Obsidian:
1. Right-click Tracks folder â†’ New folder â†’ `area-personal`
2. Inside it, create subfolders: `resources`, `tasks`, `zzz`
3. Create file: `_area-personal-home.md`
4. Use the area-home template to populate it

### 8. Start Using the System

1. **Capture** â€” Add quick notes to `Capture/`
2. **Process** â€” During reviews, convert captures to tasks or objects
3. **Work** â€” Focus on tasks in your tracks
4. **Review** â€” Update home documents with progress
5. **Archive** â€” Move completed work to `zzz/` folders

---

## Migrating Existing Vault

### Assessment

Before migrating, understand what you have:

```bash
# List your current top-level folders
ls -la /path/to/your/vault

# Count your notes
find . -name "*.md" | wc -l

# Find large directories
du -sh */ | sort -h
```

### Migration Strategy

**Option 1: Clean Start (Recommended)**
1. Set up new Cairn vault
2. Manually migrate only active/important content
3. Keep old vault as read-only archive

**Option 2: Full Migration**
1. Backup your entire existing vault
2. Add Cairn structure alongside existing content
3. Gradually migrate folders into Tracks/Objects
4. Delete old structure when migration complete

### Step-by-Step Migration

#### Phase 1: Preparation

1. **Backup everything**
   ```bash
   cp -r /path/to/vault /path/to/vault-backup-$(date +%Y%m%d)
   ```

2. **Download and unzip Cairn-PKM**
   - Get latest release from GitHub
   - Unzip to temporary location

3. **Copy Cairn structure into your vault**
   ```bash
   cp -r /path/to/cairn-extracted/_cairn-pkm /path/to/your/vault/
   mkdir -p /path/to/your/vault/_local/{data,templates,tools,views}
   mkdir -p /path/to/your/vault/{Capture,Objects,Tracks}
   ```

#### Phase 2: Content Mapping

Map your existing content to Cairn domains:

| Your Current Structure | Maps To | Example |
|------------------------|---------|---------|
| Projects folder | `Tracks/p###-*/` | Create p001, p002, etc. |
| Areas/Categories | `Tracks/area-*/` | area-work, area-personal |
| People/Contacts | `Objects/contact-*.md` | contact-john-smith.md |
| Reference materials | Track `resources/` folders | Move to relevant track |
| Inbox/Fleeting notes | `Capture/` | Move or archive old captures |
| Templates | `_local/templates/` | Your custom templates |
| Dashboards | `_local/views/` | Your custom MOCs/dashboards |

#### Phase 3: Migrate by Domain

**Start with Objects:**
```bash
# Example: Move contacts
mv contacts/john-smith.md Objects/contact-john-smith.md
mv contacts/jane-doe.md Objects/contact-jane-doe.md
```

**Then create Tracks:**
```bash
# Create first area
mkdir -p Tracks/area-work/{resources,tasks,zzz}
touch Tracks/area-work/_area-work-home.md

# Move relevant files
mv work/notes/*.md Tracks/area-work/resources/
mv work/tasks/*.md Tracks/area-work/tasks/
```

**Process Capture:**
```bash
# Move recent captures
mv inbox/*.md Capture/
# Archive or delete old captures after review
```

#### Phase 4: Update Links

After moving files, fix internal links:

1. Use Obsidian's built-in "Detect all file links" to update broken links
2. Or use search/replace for common patterns:
   ```
   Find: [[contacts/john-smith]]
   Replace: [[contact-john-smith]]
   ```

#### Phase 5: Clean Up

1. Review each old folder
2. Archive anything not migrated that you want to keep
3. Delete old folder structure
4. Commit changes if using Git

### Common Migration Patterns

**Daily Notes â†’ Capture**
```bash
mv daily-notes/*.md Capture/
# Or keep as separate daily-notes folder if you prefer
```

**Project Notes â†’ Tracks**
```bash
# Each project becomes a track
mkdir -p Tracks/p001-website-redesign/{resources,tasks,zzz}
mv projects/website-redesign/* Tracks/p001-website-redesign/resources/
```

**People â†’ Objects**
```bash
# Rename to contact-firstname-lastname format
mv people/John\ Smith.md Objects/contact-john-smith.md
```

---

## Updating Cairn-PKM

### Update Process

1. **Backup your vault**
   ```bash
   cp -r /path/to/vault /path/to/vault-backup-$(date +%Y%m%d)
   ```

2. **Download new release**
   - Get latest `cairn-vX.X.X.zip` from GitHub releases
   - Unzip to temporary location

3. **Replace _cairn-pkm folder**
   ```bash
   # Delete old version
   rm -rf /path/to/vault/_cairn-pkm
   
   # Copy new version
   cp -r /path/to/cairn-extracted/_cairn-pkm /path/to/vault/
   ```

4. **Verify everything works**
   - Open vault in Obsidian
   - Check that templates still work
   - Verify Dataview queries render correctly
   - Test LLM commands if you use them

5. **Commit changes** (if using Git)
   ```bash
   git add _cairn-pkm
   git commit -m "Update Cairn-PKM to vX.X.X"
   git push
   ```

### What Gets Updated

**Updated automatically:**
- `_cairn-pkm/templates/` â€” Shared templates
- `_cairn-pkm/llm/` â€” LLM command specifications
- `_cairn-pkm/views/` â€” Shared dashboards
- `_cairn-pkm/tools/` â€” Shared tool configurations

**Never modified:**
- Your content in `Tracks/`, `Objects/`, `Capture/`
- Your customizations in `_local/`
- Your vault settings in `.obsidian/`

### Customization Strategy

**Never edit files in `_cairn-pkm/`**

Instead, create overrides in `_local/`:

**Custom Templates:**
```
_local/templates/my-custom-template.md
```

**Custom Views:**
```
_local/views/my-dashboard.md
```

**Local Data:**
```
_local/data/changelog/
_local/data/skill-evidence/
```

This ensures your customizations survive updates.

---

## Optional: Git Setup

### For Your Vault (Not _cairn-pkm)

If you want version control for your vault content:

1. **Initialize Git**
   ```bash
   cd /path/to/vault
   git init
   ```

2. **Create .gitignore**
   ```gitignore
   # Obsidian workspace
   .obsidian/workspace*
   .obsidian/plugins/
   .trash/
   
   # System files
   .DS_Store
   Thumbs.db
   
   # Optional: Don't track local customizations
   _local/
   ```

3. **Initial commit**
   ```bash
   git add .
   git commit -m "Initial commit"
   ```

4. **Connect to GitHub** (optional)
   ```bash
   # Create repo on GitHub first, then:
   git remote add origin https://github.com/yourusername/your-vault.git
   git push -u origin main
   ```

5. **Install Obsidian Git plugin** (optional)
   - Auto-commits and pushes on a schedule
   - Provides Git commands within Obsidian

### Do NOT Track _cairn-pkm with Git

The `_cairn-pkm/` folder is a **distribution**, not a submodule:
- Updates come from replacing the folder, not git pull
- Your vault's git repo should ignore or commit it as a snapshot
- If you want to track it, commit the entire folder after each update

---

## Verification Checklist

After setup or migration, verify:

- [ ] `_cairn-pkm/` folder exists with correct structure
- [ ] `_local/` folder exists with subdirectories
- [ ] `Capture/`, `Objects/`, `Tracks/` folders exist
- [ ] At least one area created in `Tracks/`
- [ ] Dataview plugin installed and JS queries enabled
- [ ] Templater plugin installed and template folder configured
- [ ] Tasks plugin installed (optional but recommended)
- [ ] Example content reviewed and understood
- [ ] Ready to start capturing and organizing

---

## Folder Reference

### Complete Structure

```
vault/
â”œâ”€â”€ .obsidian/              # Obsidian settings (don't track in git)
â”œâ”€â”€ _cairn-pkm/             # Portable system (replace to update)
â”‚   â”œâ”€â”€ llm/               # LLM command specs
â”‚   â”‚   â”œâ”€â”€ commands/      # Individual command docs
â”‚   â”‚   â”œâ”€â”€ _ARCHITECTURE.md
â”‚   â”‚   â””â”€â”€ _INSTALLATION.md
â”‚   â”œâ”€â”€ templates/         # Shared templates
â”‚   â”œâ”€â”€ tools/             # Shared tools
â”‚   â””â”€â”€ views/             # Shared dashboards
â”œâ”€â”€ _local/                # Vault-specific customizations
â”‚   â”œâ”€â”€ data/             # Local data storage
â”‚   â”œâ”€â”€ templates/        # Custom templates
â”‚   â”œâ”€â”€ tools/            # Custom tools
â”‚   â””â”€â”€ views/            # Custom dashboards
â”œâ”€â”€ Capture/               # Inbox
â”œâ”€â”€ Objects/               # Universal entities
â””â”€â”€ Tracks/                # Areas and projects
    â”œâ”€â”€ area-{name}/
    â”‚   â”œâ”€â”€ _area-{name}-home.md
    â”‚   â”œâ”€â”€ resources/
    â”‚   â”œâ”€â”€ tasks/
    â”‚   â””â”€â”€ zzz/
    â””â”€â”€ p###-xxxx-yyyy/
        â”œâ”€â”€ _p###-xxxx-yyyy-home.md
        â”œâ”€â”€ resources/
        â”œâ”€â”€ tasks/
        â””â”€â”€ zzz/
```

---

## Naming Conventions

### Areas
`area-{domain}` â€” lowercase, hyphenated
- `area-personal`
- `area-work`
- `area-finance`
- `area-health`

### Projects
`p###-xxxx-yyyy` â€” sequential number + system code + action code
- `p001-blog-migr`
- `p002-home-reno`
- `p003-tax-2024`

To find next number:
```bash
ls Tracks/ | grep "^p" | sort | tail -1
```

### Tasks
`YYYYMMDD-slug.md`
- `20251217-fix-ssl-cert.md`
- `20251217-review-budget.md`

### Objects
`{type}-{identifier}.md`
- `contact-jane-smith.md`
- `account-github.md`
- `device-laptop-thinkpad.md`

---

## Troubleshooting

### Templates Not Working

**Problem:** Templater not expanding templates

**Solution:**
1. Settings â†’ Templater â†’ Template folder location
2. Set to: `_cairn-pkm/templates`
3. Optionally add: `_local/templates`
4. Restart Obsidian

### Dataview Queries Not Rendering

**Problem:** Dataview queries show as code blocks

**Solution:**
1. Settings â†’ Dataview
2. Enable "Enable JavaScript Queries"
3. Enable "Enable Inline Queries"
4. Refresh note (Ctrl/Cmd + R)

### Broken Links After Migration

**Problem:** Internal links broken after moving files

**Solution:**
1. Settings â†’ Files & Links
2. Enable "Automatically update internal links"
3. Or manually update links using search/replace
4. Use Obsidian's "Detect all file links" feature

### Git Conflicts with _cairn-pkm

**Problem:** Git shows changes in `_cairn-pkm/` folder

**Solution:**
- Option 1: Add `_cairn-pkm/` to `.gitignore`
- Option 2: Commit it as a snapshot after each update
- Don't try to merge changes â€” always replace entire folder

---

## Getting Help

### Documentation
- **_ARCHITECTURE.md** â€” Complete system documentation
- **Command docs** â€” `_cairn-pkm/llm/commands/cmd-*.md`
- **README.md** â€” Overview and quick reference

### Community
- GitHub Issues: Report bugs or request features
- GitHub Discussions: Ask questions, share setups

### Support the Project
- Star the repo on GitHub
- Share your experience
- Contribute improvements

---

*Version: 0.5.0 | Last Updated: 2024-12-19*
