# Cairn-PKM Architecture

Personal knowledge management system built on plain markdown files. Designed for clarity, portability, and longevity.

*Version: 0.5.0 | Last Updated: 2024-12-19*

---

## Core Principles

1. **Co-location** â€” Everything for a project lives in its folder
2. **Plain text** â€” Markdown + YAML frontmatter, no proprietary formats
3. **Two track types** â€” Areas (ongoing) and Projects (temporary)
4. **Portable core** â€” `_cairn-pkm/` can be updated by replacing the folder
5. **AI-friendly** â€” LLMs can read and work with your notes directly


## Versioning

⚠️ **Cairn-PKM is in active development (pre-release).**

All components use semantic versioning with 0.x.y format:
- **0.x.y signals**: Subject to breaking changes, interfaces may evolve
- **MINOR (0.x.0)**: New features, workflow changes
- **PATCH (0.x.y)**: Bug fixes, documentation improvements

See VERSION-POLICY.md for complete versioning rules and update guidelines.

**Current system version: 0.5.0**

Individual commands may be at different versions based on their maturity level.

---

## Five-Domain Structure

```
vault/
â”œâ”€â”€ _cairn-pkm/          # Portable system (replace to update)
â”‚   â”œâ”€â”€ llm/            # LLM command specifications
â”‚   â”œâ”€â”€ templates/      # Shared templates
â”‚   â”œâ”€â”€ tools/          # Shared tool configs
â”‚   â””â”€â”€ views/          # Shared dashboards
â”œâ”€â”€ _local/             # Vault-specific customizations
â”‚   â”œâ”€â”€ data/          # Local data storage
â”‚   â”œâ”€â”€ templates/     # Custom templates
â”‚   â”œâ”€â”€ tools/         # Custom tool configs
â”‚   â””â”€â”€ views/         # Custom dashboards
â”œâ”€â”€ Capture/            # Inbox for unprocessed items
â”œâ”€â”€ Objects/            # Cross-cutting entities
â””â”€â”€ Tracks/             # Areas and projects
    â”œâ”€â”€ area-{name}/
    â””â”€â”€ p###-xxxx-yyyy/
```

### Domain Purposes

| Domain | Purpose | Examples |
|--------|---------|----------|
| `_cairn-pkm/` | Portable system infrastructure | Templates, LLM commands, shared views |
| `_local/` | Vault-specific customizations | Personal dashboards, local data, custom templates |
| `Capture/` | Temporary inbox | Quick notes, web clips, voice memos |
| `Objects/` | Cross-cutting entities | Contacts, accounts, devices, medications |
| `Tracks/` | Areas and projects | area-finance, p045-website-redesign |

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
file_operations: "display"    # display | download | write | confirm
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
| `display` | Shows file content to copy/paste | Manual control, works anywhere |
| `download` | Creates downloadable file | Web-based LLM (Claude.ai), no filesystem access |
| `write` | Writes files directly | LLM has filesystem/Drive access, you trust it |
| `confirm` | Shows content, waits for approval, then writes | LLM has access but you want to review |

### Write Targets

When `file_operations` is `write` or `confirm`, the `write_target` determines where files go:

| Target | How It Works | Setup Required |
|--------|--------------|----------------|
| `local` | Direct filesystem write | LLM needs MCP, desktop app, or computer use access |
| `gdrive` | Google Drive API | LLM with Drive tool connected; set `gdrive_vault_path` |

**Google Drive workflow:** LLM writes to Google Drive â†’ Drive syncs to local â†’ Obsidian sees the file.

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

## Track Structure

### Areas (Ongoing Domains)

```
area-{domain}/
â”œâ”€â”€ _area-{domain}-home.md    # Source of truth
â”œâ”€â”€ resources/                # Supporting materials
â”œâ”€â”€ tasks/                    # Active work items
â””â”€â”€ zzz/                      # Completed/archived
```

**Examples:**
- `area-personal/`
- `area-work/`
- `area-finance/`
- `area-health/`

### Projects (Temporary Work)

```
p###-xxxx-yyyy/
â”œâ”€â”€ _p###-xxxx-yyyy-home.md   # Source of truth
â”œâ”€â”€ resources/                # Supporting materials
â”œâ”€â”€ tasks/                    # Project work items
â””â”€â”€ zzz/                      # Completed/archived
```

**Naming:** `p###-{system}-{action}`
- `###` = Sequential project number (001, 002, etc.)
- `{system}` = System being changed (blog, home, tax, etc.)
- `{action}` = What's being done (migr, reno, 2024, etc.)

**Examples:**
- `p001-blog-migr/`
- `p002-home-reno/`
- `p003-tax-2024/`

---

## Home Documents

Every track has a home document: `_[track-name]-home.md`

### Frontmatter Fields

```yaml
---
track_id: area-finance | p045-website
track_type: area | project
status: active | onhold | complete | archived
created: YYYY-MM-DD
updated: YYYY-MM-DD

# Project-specific fields
start_date: YYYY-MM-DD
end_date: YYYY-MM-DD
budget: "5000"
priority: low | medium | high | critical

# Area-specific fields
review_cycle: weekly | monthly | quarterly | yearly
---
```

### Required Sections

All home documents must have:

1. **Overview** â€” Purpose and scope
2. **Current State** â€” What's happening now
3. **Key Resources** â€” Important links and files
4. **Active Tasks** â€” Dataview query showing open work
5. **Log** â€” Chronological activity record

### Log Entry Format

```markdown
### YYYY-MM-DD HH:MM - {Type} - {Summary}
{Past_tense_description}
```

**Types:** Decision, Update, Milestone, Issue, Note

---

## Task Files

Located in `{track}/tasks/YYYYMMDD-{slug}.md`

### Frontmatter

```yaml
---
title: "{descriptive title}"
project: area-finance | p045-website
created_date: YYYY-MM-DD
due_date: YYYY-MM-DD
assignee: ""
parent_task: ""
priority: low | medium | high | critical
status: active | blocked | complete | deferred | onhold | scheduled | waiting
phase: planning | executing | testing | closing
effort: simple | moderate | complex
viz: "âš¡" | "ðŸ”¥" | "ðŸŽ¯" | "â¸ï¸" | etc.
type:
  - category/subcategory
last_update:
  - "YYYY-MM-DD: {action taken}"
---
```

### Body Structure

```markdown
### Task History
- YYYY-MM-DD: {action taken}

---

### Subtasks
- [ ] {subtask description}
- [ ] {subtask description}

---

### Notes
{Additional context, links, decisions}

---
```

### Parent-Child Relationships

- **Parent task:** Higher-level work item
- **Child tasks:** Specific implementation steps
- Link via `parent_task: YYYYMMDD-parent-slug.md`

### Visualization Icons (viz field)

| Icon | Meaning | When to Use |
|------|---------|-------------|
| âš¡ | Quick win | Simple, high-value tasks |
| ðŸ”¥ | Urgent | Time-sensitive work |
| ðŸŽ¯ | High priority | Important but not urgent |
| â¸ï¸ | On hold | Waiting on external dependency |
| ðŸ”’ | Blocked | Cannot proceed |
| âœ… | Complete | Finished work |

---

## Object Files

Located in `Objects/{type}-{identifier}.md` or `{track}/resources/{type}-{identifier}.md`

### Common Object Types

| Type | Filename Pattern | Example |
|------|------------------|---------|
| contact | `contact-{firstname-lastname}.md` | `contact-hazel-frost.md` |
| credit-card | `credit-card-{issuer}-{name}.md` | `credit-card-chase-sapphire.md` |
| account | `account-{institution}-{type}.md` | `account-chase-checking.md` |
| device | `device-{type}-{identifier}.md` | `device-laptop-thinkpad.md` |
| medication | `medication-{name}.md` | `medication-lisinopril.md` |
| provider | `provider-{name}.md` | `provider-dr-smith.md` |
| subscription | `subscription-{service}.md` | `subscription-netflix.md` |
| utility | `utility-{provider}.md` | `utility-pge.md` |

### Object Frontmatter

```yaml
---
type: {object-type}
status: active | inactive | archived
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
aliases: []
tags: []
# Type-specific fields below
---
```

### Placement Decision

| Scope | Location | Example |
|-------|----------|---------|
| Cross-cutting (multiple tracks) | `Objects/` | Contacts, shared devices |
| Track-specific | `{track}/resources/` | Credit cards â†’ area-finance |

---

## Capture Workflow

1. **Capture** â€” Quick notes in `Capture/`
2. **Process** â€” Review during daily/weekly reviews
3. **Decide** â€” Task, Object, Note, or Delete
4. **Act** â€” Move to appropriate track or create object
5. **Archive** â€” Delete from Capture once processed

### Capture File Naming

`qn-YYYY-MM-DD-HHMMSS-{slug}.md`

Example: `qn-2025-12-18-143022-api-integration-notes.md`

---

## Update Process

### Updating the Core System

1. Download latest release from GitHub: `cairn-vX.X.X.zip`
2. **Backup your vault first**
3. Delete your existing `_cairn-pkm/` folder
4. Copy the new `_cairn-pkm/` folder from the release
5. Verify everything still works

**Important:** Never modify files in `_cairn-pkm/` â€” put customizations in `_local/`

### Customizations

All vault-specific customizations go in `_local/`:
- **Templates:** `_local/templates/`
- **Views/Dashboards:** `_local/views/`
- **Tools/Scripts:** `_local/tools/`
- **Data:** `_local/data/` (changelogs, skill tracking, etc.)

---

## Plugin Requirements

### Required Plugins

1. **Dataview** â€” Task queries and dynamic lists
   - Enable JavaScript queries
   - Used in home documents for task summaries

2. **Templater** â€” Template expansion
   - Configure template folder: `_cairn-pkm/templates/`
   - Optionally add: `_local/templates/`

3. **Tasks** â€” Task management (optional but recommended)
   - Enhanced checkbox functionality
   - Task queries and filters

### Recommended Plugins

- **Calendar** â€” Date navigation
- **Periodic Notes** â€” Daily/weekly/monthly notes
- **QuickAdd** â€” Rapid capture workflows
- **Obsidian Git** â€” Auto-backup (for vault, not `_cairn-pkm/`)

---

## Version Control Strategy

### What to Track

**Track with Git (your vault repo):**
- `Capture/`
- `Objects/`
- `Tracks/`
- `_local/` (optional â€” contains personal customizations)
- Root-level config files

**Do NOT track:**
- `_cairn-pkm/` â€” This is the portable distribution
- `.obsidian/workspace*` â€” Workspace layouts
- `.trash/` â€” Deleted files

### .gitignore Template

```gitignore
# Obsidian
.obsidian/workspace*
.obsidian/plugins/
.trash/

# System files
.DS_Store
Thumbs.db

# Optional: Don't track local customizations
_local/
```

### Updating _cairn-pkm

Instead of git submodule (previous approach), use the **replace-folder method**:
1. Download new release
2. Delete old `_cairn-pkm/`
3. Copy new `_cairn-pkm/`
4. Commit the change

---

## Getting Started

### Quick Start

1. Download latest release zip from GitHub
2. Unzip to your desired location
3. Open in Obsidian
4. Install plugins: Dataview, Templater, Tasks
5. Explore example content in `Tracks/`, `Objects/`, `Capture/`
6. When ready, replace examples with your own content

### First Steps

1. Review example area (`area-admin`) and project (`p001-office-move`)
2. Create your first personal area: `Tracks/area-personal/`
3. Start capturing in `Capture/`
4. Process captures into tasks or objects
5. See INSTALLATION.md for detailed setup

### Growing Organically

Don't create structure you don't need yet:
- Start with one area
- Add projects as work emerges
- Add tools when you have a specific need
- Let Objects accumulate naturally

---

## Philosophy

### Why Plain Markdown?

- **Longevity** â€” Plain text outlasts every app
- **Portability** â€” Works everywhere, no export needed
- **Transparency** â€” You can see exactly what's stored
- **Version control** â€” Git works perfectly with text files
- **AI-friendly** â€” LLMs can read and work with your notes directly

### Why This Structure?

Most knowledge systems fail because they're either:
- **Too loose** â€” Everything ends up in one folder or scattered randomly
- **Too rigid** â€” Complex hierarchies that don't match how work actually flows

Cairn aims for the middle:
- **Five domains** â€” Clear top-level categories
- **Two track types** â€” Simple decision: ongoing or temporary?
- **Co-location** â€” Related things live together
- **Flexible internals** â€” Tracks can be minimal or detailed as needed

### Why Separate `_cairn-pkm/` and `_local/`?

Your *system* should evolve separately from your *customizations*:
- `_cairn-pkm/` â€” The portable distribution, updated by replacing the folder
- `_local/` â€” Your vault-specific data, custom templates, personal dashboards

This separation means:
- Improve the core system without touching your data
- Keep personal/work-specific views private
- Update by replacing one folder
- Share the system without sharing your content

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*Architecture Version: 0.5.0 | Last Updated: 2024-12-19*
