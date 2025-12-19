# Cairn-PKM Installation Guide

Instructions for setting up a new Cairn-based Obsidian vault.

## New Vault Setup

### 1. Download Cairn-PKM

1. Go to https://github.com/SFZC-WebOps/cairn-pkm/releases
2. Download the latest `cairn-vX.X.X.zip`
3. Unzip to your desired location
4. Rename the folder (optional): `Cairn-PKM-dev` → `My-Vault`

### 2. Open in Obsidian

1. Open Obsidian
2. "Open folder as vault" → Select your unzipped folder
3. Trust the author when prompted (for community plugins)

### 3. Install Required Plugins

Settings → Community Plugins → Browse:

**Required:**
- **Dataview** - For dashboards and task views
  - Settings → Dataview:
    - ✓ Enable JavaScript Queries
    - ✓ Enable Inline JavaScript Queries
- **Templater** - For file creation templates
  - Settings → Templater:
    - Template folder location: `_cairn-pkm/templates`
    - ✓ Enable "Trigger Templater on new file creation"
- **Tasks** - For task management
  - Use default settings

**Optional:**
- **Obsidian Git** - For automatic backups (if you use Git)
  - Settings → Obsidian Git:
    - Vault backup interval: 5 minutes
    - Auto pull interval: 5 minutes
    - Commit message: `vault backup: {{date}}`
    - ✓ Pull updates on startup
    - ✓ Push on backup

### 4. Explore Example Content

The vault comes with example content to show you how it works:

**Example Area:** `Tracks/area-admin/`
- Office administration area
- Contains tasks, resources, and home doc
- Shows ongoing area management

**Example Project:** `Tracks/p001-office-move/`
- Office relocation project
- Contains tasks with dependencies
- Shows time-bound project structure

**Example Objects:** `Objects/`
- Contacts (people)
- Vendors (service providers)
- Tools (software platforms)

**Example Captures:** `Capture/`
- Quick notes awaiting processing
- Shows inbox workflow

**Cross-linking:**
- Wikilinks connect tasks → objects → captures
- Demonstrates how pieces connect

### 5. Start Using It

**Don't delete examples yet!** Use them as reference while learning.

**Your first steps:**
1. Create `Tracks/area-personal/` for personal management
2. Start capturing ideas in `Capture/`
3. Create your first task in your new area
4. Process captures: turn them into tasks or objects

**When ready to clean up:**
1. Delete or archive example tracks in `Tracks/`
2. Delete or replace example objects in `Objects/`
3. Delete example captures in `Capture/`
4. Keep `_cairn-pkm/` and `_local/` intact

---

## Updating Cairn-PKM

### Getting Updates

When new versions are released:

1. Download latest `cairn-vX.X.X.zip` from GitHub releases
2. Extract the zip
3. Copy **only** the `_cairn-pkm/` folder from extracted vault
4. Replace your existing `_cairn-pkm/` folder
5. Restart Obsidian

**Your content stays safe:**
- `Tracks/` - Your areas and projects (untouched)
- `Objects/` - Your contacts, vendors, tools (untouched)
- `Capture/` - Your inbox items (untouched)
- `_local/` - Your customizations (untouched)

Only the system files in `_cairn-pkm/` are updated.

### Checking Your Version

Look at `_cairn-pkm/VERSION`:
```yaml
version: 1.0.0
released: 2025-01-18
repo: https://github.com/SFZC-WebOps/cairn-pkm
releases: https://github.com/SFZC-WebOps/cairn-pkm/releases
```

---

## Migrating Existing Notes

Have notes in another system? Here's how to bring them into Cairn:

### 1. Backup Everything First

```bash
cp -r {existing-vault} {existing-vault}-backup
```

### 2. Copy Cairn Structure

Download Cairn-PKM and copy these folders into your existing vault:
- `_cairn-pkm/` - The system
- `_local/` - For customizations
- Create: `Capture/`, `Objects/`, `Tracks/` (if they don't exist)

### 3. Move Content Gradually

| Your Content | Move To |
|--------------|---------|
| Inbox/unsorted items | `Capture/` |
| People, contacts | `Objects/contact-*.md` |
| Vendors, services | `Objects/vendor-*.md` |
| Tools, platforms | `Objects/tool-*.md` |
| Ongoing areas (health, finance) | `Tracks/area-*/` |
| Projects with end dates | `Tracks/p###-*/` |
| Personal templates | `_local/templates/` |
| Custom dashboards | `_local/views/` |

### 4. Create Home Docs

Each area/project needs a home doc:
- `Tracks/area-personal/_area-personal-home.md`
- `Tracks/p001-example/_p001-example-home.md`

Use templates from `_cairn-pkm/templates/` or copy from examples.

### 5. Organize Tasks

Move tasks into their track's `tasks/` folder:
```
Tracks/area-work/tasks/20251218-review-budget.md
Tracks/p042-renovation/tasks/20251220-get-permits.md
```

---

## Optional: Git Setup

If you want version control and automatic backups:

### 1. Initialize Git

```bash
cd {your-vault}
git init
```

### 2. Create .gitignore

```bash
cat > .gitignore << 'EOF'
# Obsidian workspace files
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/plugins/
.obsidian/community-plugins.json
.obsidian/hotkeys.json

# System files
.DS_Store
.trash/
EOF
```

### 3. Initial Commit

```bash
git add .
git commit -m "Initial vault setup with Cairn-PKM"
```

### 4. Push to GitHub (Optional)

```bash
git remote add origin https://github.com/YOUR-USERNAME/my-vault.git
git branch -M main
git push -u origin main
```

### 5. Configure Obsidian Git Plugin

Install and configure as described in step 3 above.

---

## Folder Reference

```
{Your-Vault}/
├── _cairn-pkm/              # System (update by replacing)
│   ├── ARCHITECTURE.md
│   ├── INSTALLATION.md
│   ├── VERSION
│   ├── llm/commands/
│   ├── templates/
│   ├── tools/
│   └── views/
├── _local/                  # Your customizations
│   ├── data/                # Tool output
│   ├── templates/           # Custom templates
│   ├── tools/               # Custom tool configs
│   └── views/               # Custom dashboards
├── Capture/                 # Inbox
├── Objects/                 # Universal entities
└── Tracks/                  # Areas and projects
    ├── area-{name}/
    │   ├── _area-{name}-home.md
    │   ├── resources/
    │   ├── tasks/
    │   └── zzz/
    └── p###-xxxx-yyyy/
        ├── _p###-xxxx-yyyy-home.md
        ├── resources/
        ├── tasks/
        └── zzz/
```

---

## Naming Conventions

### Areas
`area-{domain}` — lowercase, hyphenated
- `area-personal`
- `area-work`
- `area-finance`
- `area-health`

### Projects
`p###-xxxx-yyyy` — sequential number + system code + action code
- `p001-blog-migr`
- `p002-home-reno`
- `p003-tax-2024`

### Tasks
`YYYYMMDD-slug.md`
- `20251218-fix-ssl-cert.md`
- `20251218-review-budget.md`

### Objects
`{type}-{identifier}.md`
- `contact-jane-smith.md`
- `vendor-acme-hosting.md`
- `tool-obsidian.md`

---

## Plugin Configuration Quick Reference

### Dataview
```
✓ Enable JavaScript Queries
✓ Enable Inline JavaScript Queries
```

### Templater
```
Template folder location: _cairn-pkm/templates
✓ Trigger Templater on new file creation
```

### Tasks
```
Use default settings
```

### Obsidian Git (Optional)
```
Vault backup interval: 5 (minutes)
Auto pull interval: 5 (minutes)
Commit message: vault backup: {{date}}
✓ Pull updates on startup
✓ Push on backup
```

---

## Verification Checklist

After setup, verify:

- [ ] `_cairn-pkm/` folder exists with ARCHITECTURE.md
- [ ] `_local/` folder exists with data/, templates/, tools/, views/
- [ ] `Capture/`, `Objects/`, `Tracks/` folders exist
- [ ] Dataview plugin installed with JS queries enabled
- [ ] Templater plugin installed pointing to `_cairn-pkm/templates`
- [ ] Tasks plugin installed
- [ ] Can create files using Templater templates
- [ ] Example content visible and readable

---

## Getting Help

- **Documentation:** See `_cairn-pkm/llm/ARCHITECTURE.md` for complete system docs
- **Issues:** https://github.com/SFZC-WebOps/cairn-pkm/issues
- **Examples:** Explore the included example tracks, objects, and captures

---

*Version: 2.0 | Last Updated: 2024-12-18*
