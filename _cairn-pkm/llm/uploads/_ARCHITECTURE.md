# Cairn-PKM Architecture

Personal knowledge management system built on plain markdown files. Designed for clarity, portability, and longevity.

*Version: 0.5.0 | Last Updated: 2024-12-19*

---

## Core Principles

1. **Co-location** "" Everything for a project lives in its folder
2. **Plain text** "" Markdown + YAML frontmatter, no proprietary formats
3. **Two track types** "" Areas (ongoing) and Projects (temporary)
4. **Portable core** "" `_cairn-pkm/` can be updated by replacing the folder
5. **AI-friendly** "" LLMs can read and work with your notes directly


## Versioning

⚠️ **Cairn-PKM is in active development (pre-release).**

All components use semantic versioning with 0.x.y format:
- **0.x.y signals**: Subject to breaking changes, interfaces may evolve
- **MINOR (0.x.0)**: New features, workflow changes
- **PATCH (0.x.y)**: Bug fixes, documentation improvements

See _VERSION-POLICY.md for complete versioning rules and update guidelines.

**Current system version: 0.5.0**

Individual commands may be at different versions based on their maturity level.

---

## Five-Domain Structure

```
vault/
├── _cairn-pkm/          # Portable system (replace to update)
│   ├── llm/            # LLM command specifications
│   ├── templates/      # Shared templates
│   ├── tools/          # Shared tool configs
│   └── views/          # Shared dashboards
├── _local/             # Vault-specific customizations
│   ├── data/          # Local data storage
│   ├── templates/     # Custom templates
│   ├── tools/         # Custom tool configs
│   └── views/         # Custom dashboards
├── Capture/            # Inbox for unprocessed items
├── Objects/            # Cross-cutting entities
└── Tracks/             # Areas and projects
    ├── area-{name}/
    └── p###-xxxx-yyyy/
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

## Track Structure

### Areas (Ongoing Domains)

```
area-{domain}/
├── _area-{domain}-home.md    # Source of truth
├── resources/                # Supporting materials
├── tasks/                    # Active work items
└── zzz/                      # Completed/archived
```

**Examples:**
- `area-personal/`
- `area-work/`
- `area-finance/`
- `area-health/`

### Projects (Temporary Work)

```
p###-xxxx-yyyy/
├── _p###-xxxx-yyyy-home.md   # Source of truth
├── resources/                # Supporting materials
├── tasks/                    # Project work items
└── zzz/                      # Completed/archived
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

1. **Overview** "" Purpose and scope
2. **Current State** "" What's happening now
3. **Key Resources** "" Important links and files
4. **Active Tasks** "" Dataview query showing open work
5. **Log** "" Chronological activity record

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
viz: now | next | soon | later | blocked | waiting
section: ""
type:
  - category/subcategory
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

### Visualization Field (viz)

The `viz` field controls how tasks surface in dashboards and views. It's about **attention and visibility**, not task state (use `status` for state).

| Value | Meaning | When to Use |
|-------|---------|-------------|
| `now` | Active work | Working on this, needs discussion/immediate attention |
| `next` | Keep eyes on | Monitor this, up next after current work |
| `soon` | Near-term | On deck, planned for soon |
| `later` | Backlog | Eventual work, future consideration |
| `blocked` | Blocked | Cannot proceed, stuck |
| `waiting` | Waiting | External dependency, waiting on others |

**Default dashboard behavior:** Shows `now` + `next` only. Toggle to reveal full backlog.

**Note:** Task completion state belongs in the `status` field (active, complete, etc.), not `viz`.

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
| Track-specific | `{track}/resources/` | Credit cards → area-finance |

---

## Capture Workflow

1. **Capture** "" Quick notes in `Capture/`
2. **Process** "" Review during daily/weekly reviews
3. **Decide** "" Task, Object, Note, or Delete
4. **Act** "" Move to appropriate track or create object
5. **Archive** "" Delete from Capture once processed

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

**Important:** Never modify files in `_cairn-pkm/` "" put customizations in `_local/`

### Customizations

All vault-specific customizations go in `_local/`:
- **Templates:** `_local/templates/`
- **Views/Dashboards:** `_local/views/`
- **Tools/Scripts:** `_local/tools/`
- **Data:** `_local/data/` (changelogs, skill tracking, etc.)

---

## Plugin Requirements

### Required Plugins

1. **Dataview** "" Task queries and dynamic lists
   - Enable JavaScript queries
   - Used in home documents for task summaries

2. **Templater** "" Template expansion
   - Configure template folder: `_cairn-pkm/templates/`
   - Optionally add: `_local/templates/`

3. **Tasks** "" Task management (optional but recommended)
   - Enhanced checkbox functionality
   - Task queries and filters

### Recommended Plugins

- **Calendar** "" Date navigation
- **Periodic Notes** "" Daily/weekly/monthly notes
- **QuickAdd** "" Rapid capture workflows
- **Obsidian Git** "" Auto-backup (for vault, not `_cairn-pkm/`)

---

## Version Control Strategy

### What to Track

**Track with Git (your vault repo):**
- `Capture/`
- `Objects/`
- `Tracks/`
- `_local/` (optional "" contains personal customizations)
- Root-level config files

**Do NOT track:**
- `_cairn-pkm/` "" This is the portable distribution
- `.obsidian/workspace*` "" Workspace layouts
- `.trash/` "" Deleted files

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
5. See _INSTALLATION.md for detailed setup

### Growing Organically

Don't create structure you don't need yet:
- Start with one area
- Add projects as work emerges
- Add tools when you have a specific need
- Let Objects accumulate naturally

---

## Philosophy

### Why Plain Markdown?

- **Longevity** "" Plain text outlasts every app
- **Portability** "" Works everywhere, no export needed
- **Transparency** "" You can see exactly what's stored
- **Version control** "" Git works perfectly with text files
- **AI-friendly** "" LLMs can read and work with your notes directly

### Why This Structure?

Most knowledge systems fail because they're either:
- **Too loose** "" Everything ends up in one folder or scattered randomly
- **Too rigid** "" Complex hierarchies that don't match how work actually flows

Cairn aims for the middle:
- **Five domains** "" Clear top-level categories
- **Two track types** "" Simple decision: ongoing or temporary?
- **Co-location** "" Related things live together
- **Flexible internals** "" Tracks can be minimal or detailed as needed

### Why Separate `_cairn-pkm/` and `_local/`?

Your *system* should evolve separately from your *customizations*:
- `_cairn-pkm/` "" The portable distribution, updated by replacing the folder
- `_local/` "" Your vault-specific data, custom templates, personal dashboards

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
