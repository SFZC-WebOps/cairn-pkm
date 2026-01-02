# Cairn-PKM Architecture

Personal knowledge management system built on plain markdown files. Designed for clarity, portability, and longevity.

*Version: 0.5.0 | Last Updated: 2025-12-31*

---

## Core Principles

1. **LLM as reasoning engine, not storage** — The vault holds the truth. The LLM loads it, reasons over it, and produces outputs. Each conversation starts fresh from your actual data.
2. **Conversation as interface** — Instead of learning a tool's UI, you talk. Natural language is the command line.
3. **Co-location** — Everything for a project lives in its folder
4. **Plain text** — Markdown + YAML frontmatter, no proprietary formats
5. **Two track types** — Areas (ongoing) and Projects (temporary)
6. **Portable core** — `_cairn-pkm/` can be updated by replacing the folder


## Status

⚠️ **Cairn-PKM is in active development (pre-release version 0.5.0).**

Breaking changes may occur between releases. Commands and workflows may evolve as the system matures.

---

## Five-Domain Structure

```
vault/
├── .obsidian/              # Obsidian settings (don't track in git)
├── _cairn-pkm/             # Portable system (replace to update)
│   ├── llm/               # LLM command specifications
│   │   ├── commands/      # Command definitions (cmd-*.md)
│   │   ├── cmd-shared-patterns.md
│   │   ├── cmd-output-behavior.md
│   │   ├── _ARCHITECTURE.md
│   │   └── _INSTALLATION.md
│   ├── templates/         # Shared templates
│   ├── tools/             # Shared tool configs
│   └── views/             # Shared dashboards
├── _local/                # Vault-specific customizations
│   ├── data/             # Local data storage
│   ├── templates/        # Custom templates
│   ├── tools/            # Custom tool configs
│   └── views/            # Custom dashboards
├── Capture/               # Inbox for unprocessed items
├── Objects/               # Cross-cutting entities
└── Tracks/                # Areas and projects
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

Cairn uses a simple preferences file for LLM command configuration.

**Template location:** `_cairn-pkm/llm/uploads/cairn-pkm-user-prefs.yaml`

```yaml
# Identity
default_assignee: ""

# Time
timezone: "America/Los_Angeles"

# Vault location
vault_location: "local"          # local | cloud
cloud_vault_path: ""             # Required if vault_location is cloud

# Output behavior
file_operations: "download"      # download | write
```

### Settings

| Setting | Purpose | Default |
|---------|---------|---------|
| `default_assignee` | Name used in task assignee field | (empty) |
| `timezone` | IANA timezone for timestamps | America/Los_Angeles |
| `vault_location` | Where vault files are located | local |
| `cloud_vault_path` | Path to vault in cloud service | (empty) |
| `file_operations` | How LLM outputs files | download |

### Output Behavior

The `file_operations` setting controls how LLM commands handle file creation:

| Mode | Behavior | Use When |
|------|----------|----------|
| `download` | Shows content + creates downloadable file | Web-based LLM OR local filesystem (default, works anywhere) |
| `write` | Writes directly to filesystem | Desktop app with MCP/filesystem access |

**Download mode workflow:** LLM shows content on screen AND creates downloadable file → You save to vault location.

**Write mode workflow:** LLM writes directly to filesystem → File appears in vault automatically.

### Configuration Workflow

1. Edit `_cairn-pkm/llm/uploads/cairn-pkm-user-prefs.yaml` with your settings
2. Upload to your LLM project (appears at `/mnt/project/cairn-pkm-user-prefs.yaml` in Claude)
3. Commands read settings from the uploaded file

### Extending Preferences

Commands use hardcoded defaults for most settings. If you need to override them, add the setting to `cairn-pkm-user-prefs.yaml`:

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
status: planning | active | onhold | complete | archived
created: YYYY-MM-DD
updated: YYYY-MM-DD

# Project-specific fields
start_date: YYYY-MM-DD
end_date: YYYY-MM-DD
budget: "5000"
priority: low | medium | high | critical
progress: 0                   # 0-100, manually updated via !edit

# Capacity planning fields (projects)
effort_percent: 0             # 0-100, your time commitment (20 = one day/week)
peak_start: YYYY-MM-DD        # When intense phase begins
peak_end: YYYY-MM-DD          # When intense phase ends
stakeholder: ""               # Whose priority (name, department, "self")
category: operations          # operations | development | planning | event | compliance
flexibility: negotiable       # fixed | negotiable | flexible
recurrence: ""                # For cyclical work: "annual", "quarterly", "Q4"

# Area-specific fields
review_cycle: weekly | monthly | quarterly | yearly

summary: ""                   # Brief status description, updated via !edit
---
```

**Note:** See `cmd-shared-patterns.md` Field Enums section for canonical enum values.

**Progress Field (Projects):**
- Range: 0-100 (percentage)
- Manual updates only (does not auto-calculate from tasks)
- Represents overall track completion, not just task counts
- Update via: `!edit [track-home]` → `progress [0-100]`
- Optional: Can be omitted if not tracking percentage completion

**Summary Field (Projects):**
- Brief text description of current project status
- Complements progress percentage with qualitative context
- Update via: `!edit [track-home]` → `summary [text]`
- Optional: Can be omitted if not needed


**Capacity Planning Fields (Projects):**

| Field | Purpose | Notes |
|-------|---------|-------|
| `effort_percent` | Your time commitment | 20 = ~1 day/week, 100 = full-time |
| `peak_start/end` | Intense phase dates | For projects with variable intensity |
| `stakeholder` | Who this is for | Name, department, or "self" |
| `category` | Type of work | See Field Enums in cmd-shared-patterns.md |
| `flexibility` | How moveable | fixed (hard deadline), negotiable, flexible |
| `recurrence` | Cyclical pattern | "annual", "quarterly", "Q4", etc. |

These fields enable capacity analysis views showing commitment levels across time periods.

### Required Sections

All home documents must have:

1. **Overview** — Purpose and scope
2. **Current State** — What's happening now
3. **Key Resources** — Important links and files
4. **Active Tasks** — Dataview query showing open work
5. **Log** — Chronological activity record

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
status: active | blocked | complete | deferred | onhold | review | scheduled | waiting
phase: planning | executing | testing | closing
effort: simple | moderate | complex
viz: now | next | soon | later | blocked | waiting
section: ""
type:
  - category/subcategory
---
```

**Note:** See `cmd-shared-patterns.md` Field Enums section for canonical enum values.

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

### Section Field

The `section` field provides freehand grouping for view sorting. Unlike `viz` (which controls visibility) or `status` (which tracks state), `section` is a flexible label for organizing tasks within custom views.

| Usage | Description |
|-------|-------------|
| Empty (default) | Task appears in "Unsectioned" or default grouping |
| Custom text | Task groups with others sharing that section value |

**Examples:**
- `section: "Phase 1"` — Group by project phase
- `section: "Backend"` — Group by system component
- `section: "Q1 Goals"` — Group by timeframe
- `section: "Blocked Items"` — Group by workflow state

**When to use:** Add section when you need to organize tasks beyond what `viz`, `status`, or `priority` provide. Skip if the default groupings work for your view.

---

## Object Files

Located in `Objects/{type}-{identifier}.md` or `{track}/resources/{type}-{identifier}.md`

### Common Object Types

| Type | Filename Pattern | Example |
|------|------------------|---------|
| contact | `contact-{firstname-lastname}.md` | `contact-hazel-frost.md` |
| credit-card | `credit-card-{issuer}-{name}.md` | `credit-card-chase-sapphire.md` |
| account | `account-{institution}-{type}.md` | `account-chase-checking.md` |
| loan | `loan-{lender}.md` | `loan-lendingclub.md` |
| device | `device-{type}-{identifier}.md` | `device-laptop-thinkpad.md` |
| medication | `medication-{name}.md` | `medication-lisinopril.md` |
| provider | `provider-{name}.md` | `provider-dr-smith.md` |
| subscription | `subscription-{service}.md` | `subscription-netflix.md` |
| telecom | `telecom-{provider}.md` | `telecom-google-fi.md` |
| tool | `tool-{name}.md` | `tool-google-workspace.md` |
| utility | `utility-{provider}.md` | `utility-pge.md` |
| vendor | `vendor-{company-name}.md` | `vendor-acme-supplies.md` |

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

**Note:** See `cmd-shared-patterns.md` Field Enums section for canonical enum values.

### Placement Decision

| Scope | Location | Example |
|-------|----------|---------|
| Cross-cutting (multiple tracks) | `Objects/` | Contacts, shared devices |
| Track-specific | `{track}/resources/` | Credit cards → area-finance |

---

## Capture Workflow

1. **Capture** — Quick notes in `Capture/`
2. **Process** — Review during daily/weekly reviews
3. **Decide** — Task, Object, Note, or Delete
4. **Act** — Move to appropriate track or create object
5. **Archive** — Delete from Capture once processed

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

**Important:** Never modify files in `_cairn-pkm/` — put customizations in `_local/`

### Customizations

All vault-specific customizations go in `_local/`:
- **Templates:** `_local/templates/`
- **Views/Dashboards:** `_local/views/`
- **Tools/Scripts:** `_local/tools/`
- **Data:** `_local/data/` (changelogs, skill tracking, etc.)

---

## Plugin Requirements

### Required Plugins

1. **Dataview** — Task queries and dynamic lists
   - Enable JavaScript queries
   - Used in home documents for task summaries

2. **Templater** — Template expansion
   - Configure template folder: `_cairn-pkm/templates/`
   - Optionally add: `_local/templates/`

3. **Tasks** — Task management (optional but recommended)
   - Enhanced checkbox functionality
   - Task queries and filters

### Recommended Plugins

- **Calendar** — Date navigation
- **Periodic Notes** — Daily/weekly/monthly notes
- **QuickAdd** — Rapid capture workflows
- **Obsidian Git** — Auto-backup (for vault, not `_cairn-pkm/`)

---

## Version Control Strategy

### What to Track

**Track with Git (your vault repo):**
- `Capture/`
- `Objects/`
- `Tracks/`
- `_local/` (optional — contains personal customizations)
- Root-level config files

**Do NOT track:**
- `_cairn-pkm/` — This is the portable distribution
- `.obsidian/workspace*` — Workspace layouts
- `.trash/` — Deleted files

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

## LLM Commands

Cairn-PKM provides conversational commands for working with the system through LLMs like Claude. Commands are defined in `_cairn-pkm/llm/commands/` and follow shared patterns for consistency.

### Command Categories

**Onboarding:**
- `!tour` - Guided 5-minute walkthrough for new users

**Workflow Management:**
- `!hi` - Open interactive session menu or display track overview
- `!bye` - Close session with comprehensive logging and task history generation
- `!help` - Show available commands and usage

**Entity Creation & Editing:**
- `!create [type]` - Unified creation (area | project | task | object [type])
- `!edit [target]` - Unified editing (auto-detects entity type from filename or upload)

**Capture & Documentation:**
- `!capture` - Quick capture from conversation context
- `!skills` - Track skill evidence with framework-agnostic competency analysis
- `!changelog` - Document changes with YAML-structured entries

**Analysis & Planning:**
- `!capacity` - Visualize time commitments across projects, identify overcommitment periods


### How Commands Work

**Configuration:**
Commands read settings from `_local/user-prefs.yaml`:
- `file_operations` - Controls output mode (download | write)
- `default_assignee` - Default name for task assignments
- `timezone` - IANA timezone for timestamps

**Shared Patterns:**
All commands follow patterns defined in:
- `cmd-shared-patterns.md` - Common initialization, completion, error handling
- `cmd-output-behavior.md` - File output modes and fallback chains

**Robustness Principles:**
- **Verify-Before-Modify (VBM)** - Confirm state before write operations
- **Graceful Fallback Chain (GFC)** - Degrade safely (write → download → display)
- **Rationale Capture (RC)** - Log the "why" behind changes, not just "what"

### Session Model

Commands support session-based workflows:
1. `!hi` - Opens session context (menu or track overview)
2. Work via commands - Context maintained across interactions
3. `!bye` - Generates session log with task history and change summary

Session context tracks files created/edited, decisions made, and primary track focus for accurate logging.

### Template System

Object creation uses flexible template discovery:
1. `_local/templates/object-{type}.md` (user customs - highest priority)
2. `_cairn-pkm/templates/object-{type}.md` (core built-ins)
3. `_cairn-pkm/templates/examples/object-{type}.md` (examples)
4. Base template (generic fallback)

This allows custom object types while providing sensible defaults.

**Note on Templates:** If templates are not present in your vault, the system will use base templates with standard fields. Template files are not required for the system to function - they provide customization when needed.

### Command Documentation

Each command has a specification file (cmd-*.md) with:
- Quick reference and workflow overview
- Execution phases with pseudo-code
- Template formats and examples
- Error handling patterns
- Version history

See individual command files in `_cairn-pkm/llm/commands/` for complete details.

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

1. Review example area (`area-admin`) and project (`p002-prod-lnch`)
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

- **Longevity** — Plain text outlasts every app
- **Portability** — Works everywhere, no export needed
- **Transparency** — You can see exactly what's stored
- **Version control** — Git works perfectly with text files
- **AI-friendly** — LLMs can read and work with your notes directly

### Why This Structure?

Most knowledge systems fail because they're either:
- **Too loose** — Everything ends up in one folder or scattered randomly
- **Too rigid** — Complex hierarchies that don't match how work actually flows

Cairn aims for the middle:
- **Five domains** — Clear top-level categories
- **Two track types** — Simple decision: ongoing or temporary?
- **Co-location** — Related things live together
- **Flexible internals** — Tracks can be minimal or detailed as needed

### Why Separate `_cairn-pkm/` and `_local/`?

Your *system* should evolve separately from your *customizations*:
- `_cairn-pkm/` — The portable distribution, updated by replacing the folder
- `_local/` — Your vault-specific data, custom templates, personal dashboards

This separation means:
- Improve the core system without touching your data
- Keep personal/work-specific views private
- Update by replacing one folder
- Share the system without sharing your content

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*Architecture Version: 0.5.0 | Last Updated: 2025-12-31*
