# Cairn-PKM Documentation

A comprehensive guide to the Cairn-PKM personal knowledge management system.

**Version:** 1.0.0  
**Last Updated:** December 18, 2025

---

## What is Cairn-PKM?

Cairn-PKM is an opinionated folder structure for organizing personal and professional knowledge in plain markdown files. Like the stone trail markers (cairns) that guide hikers through unfamiliar terrain, Cairn provides just enough structure to keep you oriented without getting in your way.

### Core Features

- **Plain text** — Markdown files with YAML frontmatter. No lock-in.
- **Co-location** — Everything for a project lives in its folder.
- **Portable core** — The system (`_cairn-pkm/`) can be shared across vaults.
- **Local customization** — Vault-specific stuff stays in `_local/`, never shared.
- **Two track types** — Areas (ongoing) and Projects (temporary). Nothing in between.

---

## Quick Navigation

| Section | Description |
|---------|-------------|
| [Getting Started](#getting-started) | Installation and first steps |
| [System Architecture](architecture.md) | How Cairn-PKM is organized |
| [Folder Structure](folders.md) | The five domains explained |
| [Home Docs](home-docs.md) | Track source of truth |
| [Tasks](tasks.md) | Task file format and usage |
| [Commands](commands/index.md) | LLM command reference |
| [Templates](templates/index.md) | Templater templates |
| [Tools](tools/index.md) | Changelog and Skill Tracker |
| [Views](views/index.md) | Dataview dashboards |

---

## Getting Started

### Prerequisites

- [Obsidian](https://obsidian.md) (recommended)
- [Templater](https://github.com/SilentVoid13/Templater) plugin (for templates)
- [Dataview](https://github.com/blacksmithgu/obsidian-dataview) plugin (for views)

### Quick Setup

1. Download/clone Cairn-PKM into your vault as `_cairn-pkm/`
2. Create these folders: `_local/`, `Capture/`, `Objects/`, `Tracks/`
3. Configure Templater to use `_cairn-pkm/templates/`
4. Create your first area: `Tracks/area-personal/`

For detailed setup, see [architecture.md](architecture.md).

---

## Folder Structure

```
{Vault}/
├── _cairn-pkm/           # The system (portable)
│   ├── llm/
│   │   ├── ARCHITECTURE.md
│   │   ├── INSTALLATION.md
│   │   └── commands/     # LLM command specs
│   ├── templates/        # Templater templates
│   ├── tools/            # Tool definitions
│   └── views/            # Dataview dashboards
├── _local/               # Your customizations
│   ├── data/             # Tool output
│   ├── templates/        # Custom templates
│   └── views/            # Custom dashboards
├── Capture/              # Inbox
├── Objects/              # Universal entities
└── Tracks/               # Areas and projects
    ├── area-{name}/      # Ongoing areas
    └── p###-xxxx-yyyy/   # Time-bound projects
```

---

## Track Types

### Areas (Ongoing)

Named `area-{domain}` — things you maintain indefinitely.

**Examples:**
- `area-finance` — Personal finances
- `area-health` — Health and fitness
- `area-webops` — Web operations work

### Projects (Temporary)

Named `p###-xxxx-yyyy` — things with a finish line.

- `p` = project prefix
- `###` = sequential number (001-999)
- `xxxx` = system code (4 letters)
- `yyyy` = action code (4 letters)

**Examples:**
- `p001-blog-migr` — Blog migration project
- `p042-tax-2024` — 2024 tax preparation

---

## Key Concepts

### Co-location

Everything for a track (project or area) lives in one folder:

```
Tracks/p001-blog-migr/
├── _p001-blog-migr-home.md   # Source of truth
├── resources/                 # Supporting docs
├── tasks/                     # Task files
└── zzz/                       # Archive
```

### Home Docs

Every track has a home doc (`_{track}-home.md`) containing:
- Overview and purpose
- Current focus
- Log of activities
- Status and progress

### Task Files

Individual markdown files in `tasks/` folder:
- Filename: `YYYYMMDD-slug.md`
- YAML frontmatter for metadata
- Body with subtasks and notes

---

## Documentation Index

### Structure

Core system organization:

| Document | Description |
|----------|-------------|
| [Folder Structure](folders.md) | The five domains (_cairn-pkm, _local, Capture, Objects, Tracks) |
| [Home Docs](home-docs.md) | Track source of truth, frontmatter, log entries |
| [Tasks](tasks.md) | Task file format, fields, subtasks, queries |

### Commands

LLM command specifications for AI-assisted workflows:

| Command | Purpose | Type |
|---------|---------|------|
| [!hi](commands/cmd-hi.md) | Work overview | Read |
| [!bye](commands/cmd-bye.md) | Session closure | Display |
| [!task](commands/cmd-task.md) | Task management | Display |
| [!qn](commands/cmd-qn.md) | Quick note capture | Write |
| [!change](commands/cmd-change.md) | Change tracking | Write |
| [!obj](commands/cmd-obj.md) | Object management | Display/Write |
| [!readme](commands/cmd-readme.md) | Reference research | Read/Write |
| [!sk](commands/cmd-sk.md) | Skill evidence scraping | Display |

### Templates

Templater templates for file creation:

| Template | Purpose |
|----------|---------|
| [capture-quick](templates/capture-quick.md) | Quick inbox capture |
| [task](templates/task.md) | New task file |
| [track-project](templates/track-project.md) | New project |
| [track-area](templates/track-area.md) | New area |
| [object](templates/object.md) | Universal object |
| [object-financial](templates/object-financial.md) | Financial object |
| [review-weekly](templates/review-weekly.md) | Weekly review |

### Tools

Specialized workflows:

| Tool | Purpose |
|------|---------|
| [Changelog](tools/changelog.md) | Track production changes |
| [Skill Tracker](tools/skill-tracker.md) | Professional development |

### Views

Dataview dashboards:

| View | Purpose |
|------|---------|
| [tasks-dashboard](views/tasks-dashboard.md) | Main task view |
| [tracks-viewer](views/tracks-viewer.md) | Project/area browser |
| [tasks-overdue](views/tasks-overdue.md) | Overdue task alerts |
| [tracks-projects](views/tracks-projects.md) | Active projects |
| [capture-inbox](views/capture-inbox.md) | Inbox status |
| [review-weekly](views/review-weekly.md) | Weekly dashboard |

---

## License

MIT License. See [LICENSE](https://github.com/SFZC-WebOps/cairn-pkm) for details.

---

*Documentation generated December 18, 2025*
