# CAIRN Installation Guide

Instructions for setting up a new CAIRN-based Obsidian vault or migrating an existing vault.

## New Vault Setup

### 1. Create the vault folder structure

```bash
mkdir -p {vault-name}
cd {vault-name}

# Create the five domains
mkdir -p _local/data _local/templates _local/tools _local/views
mkdir Capture Objects Tracks
```

### 2. Add CAIRN as a git submodule

```bash
# Initialize git
git init

# Add CAIRN (private repo - requires access)
git submodule add https://github.com/SFZC-WebOps/cairn-pkm.git _CAIRN

# Initial commit
git add .
git commit -m "Initial vault setup with CAIRN"
```

### 3. Create your first area

```bash
mkdir -p Tracks/area-personal/resources Tracks/area-personal/tasks Tracks/area-personal/zzz
```

Create `Tracks/area-personal/_area-personal-home.md`:
```markdown
---
title: Personal
type: area
status: active
created: YYYY-MM-DD
modified: YYYY-MM-DD
---

## Overview

Personal life management.

## Current Focus

[What's active now]

## Notes

[Working scratchpad]

---

## Log

---

YYYY-MM-DD - Created area
```

### 4. Configure Obsidian

Open the folder as an Obsidian vault, then:

**Install Community Plugins:**

Settings → Community Plugins → Browse:
- **Templater** (required for `_CAIRN/templates/`)
- **Dataview** (required for `_CAIRN/views/`)
- **Tasks** (required for task management in `Tracks/*/tasks/`)
- **Obsidian Git** (recommended for version control)

**Configure Templater:**

Settings → Templater:
- Template folder location: `_CAIRN/templates`
- ✓ Enable "Trigger Templater on new file creation"

**Configure Dataview:**

Settings → Dataview:
- ✓ Enable JavaScript Queries (required for dashboard views)
- ✓ Enable Inline JavaScript Queries

**Configure Obsidian Git (Optional):**

Settings → Obsidian Git:
- Auto-pull interval: 10 minutes (or your preference)
- Auto-save interval: 5 minutes (or your preference)
- Commit message: "vault backup: {{date}}"

### 5. Create .gitignore

```bash
cat > .gitignore << 'EOF'
# Obsidian
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/plugins/
.obsidian/community-plugins.json
.obsidian/hotkeys.json

# System
.DS_Store
.trash/

# Optional: exclude _local if you don't want to track customizations
# _local/
EOF
```

### 6. Push to GitHub (optional)

```bash
git remote add origin https://github.com/YOUR_ORG/{vault-name}.git
git add .
git commit -m "Initial vault setup"
git push -u origin main
```

---

## Migrating an Existing Vault

### 1. Backup first

```bash
cp -r {existing-vault} {existing-vault}-backup
```

### 2. Create new structure alongside existing content

```bash
cd {existing-vault}

# Create CAIRN folders (won't affect existing content)
mkdir -p _local/data _local/templates _local/tools _local/views
mkdir -p Capture Objects Tracks
```

### 3. Add CAIRN submodule

```bash
# Initialize git if needed
git init

# Add CAIRN
git submodule add https://github.com/SFZC-WebOps/cairn-pkm.git _CAIRN
```

### 4. Migrate content gradually

Move existing content into CAIRN structure:

| Existing Content | Move To |
|------------------|---------|
| Inbox/unprocessed items | `Capture/` |
| People, contacts, accounts | `Objects/` |
| Ongoing life areas | `Tracks/area-{name}/` |
| Projects with end dates | `Tracks/p###-xxxx-yyyy/` |
| Personal templates | `_local/templates/` |
| Personal dashboards | `_local/views/` |

### 5. Create home docs for each track

Each area/project needs a home doc:
- `Tracks/area-{name}/_area-{name}-home.md`
- `Tracks/p###-xxxx-yyyy/_p###-xxxx-yyyy-home.md`

Use templates from `_CAIRN/templates/` or create manually.

### 6. Move tasks into track folders

Tasks should live in their track's `tasks/` folder:
```
Tracks/area-work/tasks/20251201-quarterly-review.md
Tracks/p042-blog-migr/tasks/20251215-dns-cutover.md
```

---

## Updating CAIRN

### Pulling Updates to Your Vault

When the cairn-pkm repository is updated (new templates, commands, etc.), update your vault:

```bash
cd {vault}/_CAIRN
git pull origin main
cd ..
git add _CAIRN
git commit -m "Update CAIRN submodule"
git push
```

### Making Changes to CAIRN Itself

If you need to modify CAIRN system files (templates, commands, etc.):

1. **Edit in the source repository:**
   ```bash
   cd {path-to-cairn-pkm-repo}
   # Make your changes
   git add .
   git commit -m "Description of changes"
   git push
   ```

2. **Update all vaults using CAIRN:**
   ```bash
   cd {vault}/_CAIRN
   git pull origin main
   cd ..
   git add _CAIRN
   git commit -m "Update CAIRN submodule - description of changes"
   git push
   ```

Repeat step 2 for each vault that uses CAIRN.

---

## Folder Reference

```
{Vault}/
├── _CAIRN/              # System (git submodule, don't edit)
│   ├── ARCHITECTURE.md
│   ├── llm/commands/
│   ├── templates/
│   ├── tools/
│   └── views/
├── _local/              # Your customizations (vault-specific)
│   ├── data/            # Tool output (changelog entries, etc.)
│   ├── templates/       # Custom templates
│   ├── tools/           # Custom tool configs
│   └── views/           # Custom dashboards
├── Capture/             # Inbox
├── Objects/             # Universal entities
└── Tracks/              # Areas and projects
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
- `vendor-acme-hosting.md`
- `account-github.md`

---

## Verification Checklist

After setup, verify:

- [ ] `_CAIRN/` folder exists with ARCHITECTURE.md
- [ ] `_local/` folder exists with data/, templates/, tools/, views/
- [ ] `Capture/`, `Objects/`, `Tracks/` folders exist
- [ ] At least one area created in `Tracks/`
- [ ] Templater plugin installed and configured
- [ ] Dataview plugin installed with JS queries enabled
- [ ] Tasks plugin installed
- [ ] Git submodule working (`cd _CAIRN && git status`)

---

*Version: 1.0 | Last Updated: 2025-12-17*
