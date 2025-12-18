# Cairn-PKM System Architecture

A markdown-based personal knowledge management system designed for clarity, portability, and longevity.

## What is Cairn-PKM?

Cairn-PKM is an opinionated folder structure for organizing personal and professional knowledge in plain markdown files. It works with [Obsidian](https://obsidian.md), any text editor, or anything that reads folders and files.

Like the stone trail markers (cairns) that guide hikers through unfamiliar terrain, Cairn provides just enough structure to keep you oriented without getting in your way.

**Core idea:** Your knowledge system should be simple enough to understand in five minutes, flexible enough to last decades, and portable enough to survive any tool.

### Who is this for?

- People who want structure without lock-in
- Knowledge workers managing projects and ongoing responsibilities
- Developers who prefer files over databases
- Anyone tired of rebuilding their "system" every year

### Design principles

1. **Co-location** — Everything for a project lives in its folder. No mental mapping required.
2. **Plain text** — Markdown files, YAML frontmatter. Readable by humans and machines forever.
3. **Portable core** — The system itself (`_CAIRN/`) is a git submodule you can update across vaults.
4. **Local customization** — Your vault-specific stuff stays in `_local/`, never shared.
5. **Minimal structure** — Start with folders. Add complexity only when needed.
6. **Two track types** — Areas (ongoing) and Projects (temporary). Nothing in between.

---

## The Five Domains

Every Cairn vault has five top-level folders:

```
{Vault}/
├── _CAIRN/      # The system (portable, git submodule)
├── _local/      # Your customizations (vault-specific)
├── Capture/     # Inbox for unprocessed items
├── Objects/     # Universal entities (people, accounts, tools)
└── Tracks/      # Where work happens (areas and projects)
```

| Folder | Purpose | Portable? |
|--------|---------|-----------|
| `_CAIRN/` | System templates, views, tool definitions | Yes — same across all vaults |
| `_local/` | Custom templates, views, tool data | No — this vault only |
| `Capture/` | Inbox, quick notes, unprocessed items | No — vault-specific |
| `Objects/` | Entities that cross project boundaries | No — vault-specific |
| `Tracks/` | Active work — areas and projects | No — vault-specific |

The two underscore-prefixed folders are system infrastructure. The other three are your content.

---

## _CAIRN/ — The Portable System

The underscore prefix sorts it to the top and signals "infrastructure, not content."

```
_CAIRN/
├── LICENSE
├── README.md
├── ARCHITECTURE.md
├── llm/
│   └── commands/       # LLM command specs
├── templates/          # Templater templates for file creation
├── tools/              # Tool definitions
│   └── {tool-name}/
│       └── tool.md     # How this tool works
└── views/              # Dataview queries, dashboards
```

### Distribution model

`_CAIRN/` is a **git submodule**. Every vault points to the same repository.

```bash
# Add to a new vault
git submodule add https://github.com/you/cairn-pkm.git _CAIRN

# Update to latest
cd _CAIRN && git pull
```

This means:
- Improve a template once, update everywhere
- Version history for your system
- Same tools and views across all vaults
- No manual syncing

### What lives in _CAIRN/ vs. _local/

| `_CAIRN/` (portable) | `_local/` (vault-specific) |
|----------------------|---------------------------|
| Default templates | Custom templates |
| Default views/dashboards | Custom dashboards |
| Tool definitions (`tool.md`) | Tool data output |
| LLM command specs | — |
| System documentation | — |

---

## _local/ — Your Customizations

Vault-specific system files. Never shared, never in the git submodule.

```
_local/
├── data/           # Tool output (changelog entries, skill evidence, etc.)
├── templates/      # Custom templates for this vault
├── tools/          # Custom tool configurations
└── views/          # Custom dashboards and queries
```

### The data/ folder

Where tools write their accumulated data. Structure mirrors `_CAIRN/tools/`:

```
_local/data/
├── changelog/
│   └── {entries}
├── professional-development/
│   └── skill-evidence.md
└── {other-tools}/
```

The separation:
- `_CAIRN/tools/changelog/tool.md` — How the changelog works (portable)
- `_local/data/changelog/` — Your actual changelog entries (vault-specific)

This keeps tool definitions separate from the data they generate.

### Custom templates and views

If you need templates or dashboards specific to your work (job-specific, personal workflows), put them in `_local/templates/` or `_local/views/`. They won't pollute the portable distribution.

---

## Capture/ — The Inbox

Where unprocessed items land. The goal is regularly processing to zero.

Each item either:
- Becomes a **task** → moves to a track's `tasks/` folder
- Becomes **reference** → moves to `Objects/` or a track's `resources/`
- Gets **deleted** → wasn't worth keeping

File naming is loose here — it's temporary storage:
- `20250115-143022-meeting-idea.md`
- `screenshot-2025-01-15.png`
- `random-thought.md`

---

## Objects/ — Universal Entities

Things that exist independent of any project. Entities you reference from multiple places.

```
Objects/
├── contact-jane-smith.md
├── vendor-acme-hosting.md
├── tool-rclone.md
├── account-github.md
└── subscription-netflix.md
```

### Type prefixes

| Prefix | Use for |
|--------|---------|
| `contact-` | People |
| `vendor-` | Service providers |
| `tool-` | Software and utilities |
| `account-` | Accounts and credentials |
| `subscription-` | Recurring services |
| `device-` | Hardware, equipment |
| `location-` | Places |

### When to use Objects vs. track resources

| Put in `Objects/` | Put in track `resources/` |
|-------------------|--------------------------|
| Referenced by multiple tracks | Only relevant to one track |
| Exists independent of any project | Created for/by this project |
| Long-lived entity | Project-specific documentation |

---

## Tracks/ — Where Work Happens

Two track types, identical internal structure:

```
Tracks/
├── area-{domain}/        # Ongoing, no end date
│   ├── _area-{domain}-home.md
│   ├── resources/
│   ├── tasks/
│   └── zzz/
└── p###-aaaa-bbbb/       # Time-bound, has an end
    ├── _p###-aaaa-bbbb-home.md
    ├── resources/
    ├── tasks/
    └── zzz/
```

### Areas vs. Projects

| Aspect | Areas | Projects |
|--------|-------|----------|
| Duration | Ongoing, no end date | Temporary, has completion criteria |
| Naming | `area-{domain}` | `p###-aaaa-bbbb` |
| Examples | `area-finance`, `area-health` | `p042-blog-migr`, `p043-tax-2024` |
| When done | Never (archive if life changes) | Archive to `zzz/` or delete |

### Track anatomy

Every track follows the same pattern:

```
{track}/
├── _{track}-home.md    # Source of truth
├── resources/          # Supporting documents
├── tasks/              # Task files
└── zzz/                # Archive
```

#### Home doc (`_{track}-home.md`)

The underscore sorts it first. Contains everything about the track:

```markdown
---
project_id: p###-aaaa-bbbb
title: Full Project Name
type: project
status: active
progress: 0-100
created: YYYY-MM-DD
modified: YYYY-MM-DD
---

## Overview
What this is, 2-3 sentences.

## Current Focus
What's active right now.

## Notes
Working scratchpad — open questions, pending decisions.

---

## Log

---

YYYY-MM-DD HH:MM - Type - Description
Details if needed.

---

YYYY-MM-DD HH:MM - Type - Description
Details if needed.
```

**Log entry types:** Development, Configuration, Documentation, Meeting, Decision, BLOCKED, Testing, Deployment, Review

**Log format:** Newest at bottom (append-only). Entries separated by horizontal rules.

#### Resources folder

Track-specific documents. Things that belong to *this* track, not universal entities.

#### Tasks folder

Task files for this track. Co-located with the work, queryable across all tracks.

#### Archive (`zzz/`)

Completed sub-efforts, obsolete references, historical records. The `zzz` name sorts it to the bottom.

---

## Task Management

Tasks are markdown files in `{track}/tasks/`.

### Task file structure

Filename pattern: `YYYYMMDD-slug.md`

```markdown
---
title: Task title
project: p###-aaaa-bbbb
created_date: YYYY-MM-DD
due_date: YYYY-MM-DD
assignee: Name
priority: low | medium | high | critical
status: active | blocked | complete | deferred | onhold | scheduled | waiting
phase: planning | executing | testing | closing
effort: simple | moderate | complex
---

## Task History
- YYYY-MM-DD: Created task - brief summary
- YYYY-MM-DD: Status update - what changed

---

## Subtasks
- [ ] First subtask
- [ ] Second subtask
- [x] Completed subtask

---

## Notes

Working notes, context, links.
```

### Querying across tracks

With Dataview:

```dataview
TABLE status, priority, due_date
FROM "Tracks"
WHERE contains(file.folder, "tasks")
AND status != "complete"
SORT priority DESC
```

### External task systems

For time-sensitive items needing reminders, use external tools (calendar, Todoist) alongside. CAIRN tasks are for tracking and context, not notifications.

---

## Naming Conventions

### Tracks

| Type | Pattern | Example |
|------|---------|---------|
| Area | `area-{domain}` | `area-finance`, `area-webops` |
| Project | `p###-aaaa-bbbb` | `p013-blog-migr`, `p042-tax-2024` |

### Project ID system

Format: `p` + sequential number + system code + action code

- `p` = project prefix
- `###` = sequential number (001-999, zero-padded)
- `aaaa` = 4-letter system code (readable)
- `bbbb` = 4-letter action code (readable)

**System codes:** `blog`, `home`, `work`, `hlth` (health), `finc` (finance)

**Action codes:** `migr` (migration), `audt` (audit), `docs` (documentation), `setup`

Rules:
- Never reuse numbers
- Always lowercase
- Codes should be readable without a lookup table

### Files

| Type | Pattern | Example |
|------|---------|---------|
| Home doc | `_{track}-home.md` | `_p013-blog-migr-home.md` |
| Task | `YYYYMMDD-slug.md` | `20251102-fix-ssl-cert.md` |
| Object | `{type}-{identifier}.md` | `contact-jane-smith.md` |
| Tool definition | `tool.md` | (lowercase) |

### Casing

- **Uppercase:** Top-level info docs — `README.md`, `ARCHITECTURE.md`, `LICENSE`
- **Lowercase:** Everything else — folders, command specs, tool definitions, all other files

---

## Integration Points

CAIRN works with various tools. None are required.

| Tool | Role | Required? |
|------|------|-----------|
| [Obsidian](https://obsidian.md) | Primary interface for viewing/editing | No |
| [Templater](https://github.com/SilentVoid13/Templater) | File creation with consistent structure | No |
| [Dataview](https://github.com/blacksmithgu/obsidian-dataview) | Cross-vault queries | No |
| Git | Version control, submodule for `_CAIRN/` | Recommended |
| Any text editor | Reading and writing markdown | Yes |

### LLM integration

CAIRN's plain-text structure works well with AI assistants. Command specifications (in `_CAIRN/llm/commands/`) can be loaded into your LLM's context to automate common workflows.

---

## Getting Started

### Minimal setup

1. Create the folder structure:
   ```
   mkdir _CAIRN _local Capture Objects Tracks
   mkdir _local/data _local/templates _local/tools _local/views
   ```

2. Create your first area:
   ```
   mkdir -p Tracks/area-personal/resources Tracks/area-personal/tasks Tracks/area-personal/zzz
   touch Tracks/area-personal/_area-personal-home.md
   ```

3. Start capturing and processing.

### With git submodule

1. Initialize your vault as a git repo:
   ```bash
   git init
   ```

2. Add `_CAIRN` as a submodule:
   ```bash
   git submodule add https://github.com/you/cairn-pkm.git _CAIRN
   ```

3. Create `_local/` and the content folders:
   ```bash
   mkdir -p _local/data _local/templates _local/tools _local/views
   mkdir Capture Objects Tracks
   ```

4. Add `_local/` to your vault's `.gitignore` if you don't want to track personal customizations.

### Growing organically

Don't create structure you don't need yet:
- Start with one area
- Add projects as work emerges
- Add tools when you have a specific need
- Let Objects accumulate naturally

---

## Philosophy

### Why plain markdown?

- **Longevity** — Plain text outlasts every app
- **Portability** — Works everywhere, no export needed
- **Transparency** — You can see exactly what's stored
- **Version control** — Git works perfectly with text files
- **AI-friendly** — LLMs can read and work with your notes directly

### Why this structure?

Most knowledge systems fail because they're either:
- Too loose — Everything ends up in one folder or scattered randomly
- Too rigid — Complex hierarchies that don't match how work actually flows

CAIRN aims for the middle:
- **Five domains** — Clear top-level categories
- **Two track types** — Simple decision: ongoing or temporary?
- **Co-location** — Related things live together
- **Flexible internals** — Tracks can be minimal or detailed as needed

### Why separate `_CAIRN/` and `_local/`?

Your *system* should evolve separately from your *customizations*:
- `_CAIRN/` — The portable distribution, shared across vaults, updated via git
- `_local/` — Your vault-specific data, custom templates, personal dashboards

This separation means:
- Improve the core system without touching your data
- Keep personal/work-specific views private
- Update across multiple vaults with `git pull`
- Share the system without sharing your content

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

*Architecture Version: 4.0*
