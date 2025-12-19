# Cairn-PKM System Architecture

A deep dive into how Cairn-PKM is organized and why.

---

## The Five Domains

Every Cairn vault has five top-level folders:

| Folder | Purpose | Portable? |
|--------|---------|-----------|
| `_cairn-pkm/` | System templates, views, tool definitions | Yes |
| `_local/` | Custom templates, views, tool data | No |
| `Capture/` | Inbox, quick notes, unprocessed items | No |
| `Objects/` | Entities that cross project boundaries | No |
| `Tracks/` | Active work — areas and projects | No |

The two underscore-prefixed folders are system infrastructure. The other three are your content.

---

## _cairn-pkm/ — The Portable System

```
_cairn-pkm/
├── LICENSE
├── README.md
├── VERSION
├── llm/
│   ├── ARCHITECTURE.md
│   ├── INSTALLATION.md
│   └── commands/         # LLM command specs
├── templates/            # Templater templates
├── tools/                # Tool definitions
└── views/                # Dataview dashboards
```

### Update Process

To update Cairn-PKM:
1. Download latest release from GitHub
2. Replace your existing `_cairn-pkm/` folder
3. Your content in other folders stays untouched

---

## _local/ — Your Customizations

```
_local/
├── data/           # Tool output (changelog, skill evidence)
├── templates/      # Custom templates for this vault
├── tools/          # Custom tool configurations
└── views/          # Custom dashboards and queries
```

### Tool Data Storage

Where tools write their output:

```
_local/data/
├── changelog/
│   └── YYYY/
│       └── MM/
│           └── CHG-YYYYMMDD-HHMMSS.yaml
└── skill-tracker/
    └── skill-evidence.md
```

---

## Capture/ — The Inbox

Where unprocessed items land. Process to zero regularly.

Each item either:
- Becomes a **task** → moves to a track's `tasks/` folder
- Becomes **reference** → moves to `Objects/` or track `resources/`
- Gets **deleted** → wasn't worth keeping

---

## Objects/ — Universal Entities

Things that exist independent of any project.

### Type Prefixes

| Prefix | Use for |
|--------|---------|
| `contact-` | People |
| `vendor-` | Service providers |
| `tool-` | Software and utilities |
| `account-` | Accounts and credentials |
| `subscription-` | Recurring services |
| `device-` | Hardware, equipment |
| `location-` | Places |

### When to Use Objects vs. Track Resources

| Put in `Objects/` | Put in track `resources/` |
|-------------------|--------------------------|
| Referenced by multiple tracks | Only relevant to one track |
| Exists independent of any project | Created for/by this project |
| Long-lived entity | Project-specific documentation |

---

## Tracks/ — Where Work Happens

### Track Types

| Type | Pattern | Duration | Example |
|------|---------|----------|---------|
| Area | `area-{domain}` | Ongoing | `area-finance` |
| Project | `p###-xxxx-yyyy` | Temporary | `p042-blog-migr` |

### Track Anatomy

Every track follows the same pattern:

```
{track}/
├── _{track}-home.md    # Source of truth
├── resources/          # Supporting documents
├── tasks/              # Task files
└── zzz/                # Archive
```

### Home Doc Structure

```markdown
---
project_id: p###-xxxx-yyyy
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
Working scratchpad.

---

## Log

---

YYYY-MM-DD HH:MM - Type - Description
```

---

## Task Management

Tasks are markdown files in `{track}/tasks/`.

### Task File Structure

**Filename:** `YYYYMMDD-slug.md`

```markdown
---
title: Task title
project: p###-xxxx-yyyy
created_date: YYYY-MM-DD
due_date: YYYY-MM-DD
assignee: Name
priority: low | medium | high | critical
status: active | blocked | complete | deferred | onhold | scheduled | waiting
phase: planning | executing | testing | closing
effort: simple | moderate | complex
viz: 11 | eyeson | backlog | scheduled | system | reviewing
---

## Task History
- YYYY-MM-DD: Created task - brief summary

---

## Subtasks
- [ ] First subtask
- [x] Completed subtask

---

## Notes
```

### Viz Field Values

| Value | Purpose |
|-------|---------|
| `11` | High priority, must see today |
| `eyeson` | Active attention needed |
| `backlog` | Waiting in backlog |
| `scheduled` | Scheduled for future |
| `system` | System/maintenance tasks |
| `reviewing` | Under review |

---

## Naming Conventions

### Tracks

| Type | Pattern | Example |
|------|---------|---------|
| Area | `area-{domain}` | `area-finance` |
| Project | `p###-aaaa-bbbb` | `p013-blog-migr` |

### Files

| Type | Pattern | Example |
|------|---------|---------|
| Home doc | `_{track}-home.md` | `_p013-blog-migr-home.md` |
| Task | `YYYYMMDD-slug.md` | `20251218-fix-ssl-cert.md` |
| Object | `{type}-{identifier}.md` | `contact-jane-smith.md` |

### Casing

- **Uppercase:** Info docs — `README.md`, `ARCHITECTURE.md`, `LICENSE`
- **Lowercase:** Everything else

---

## Design Principles

1. **Co-location** — Related things live together
2. **Plain text** — Markdown files, YAML frontmatter
3. **Portable core** — System can be updated by replacing one folder
4. **Local customization** — Your stuff stays separate
5. **Minimal structure** — Start simple, add complexity when needed
6. **Two track types** — Areas (ongoing) or Projects (temporary)

---

## Integration Points

| Tool | Role | Required? |
|------|------|-----------|
| Obsidian | Primary interface | No |
| Templater | File creation | No |
| Dataview | Cross-vault queries | No |
| Tasks | Task management | No |
| Any text editor | Reading/writing markdown | Yes |

---

*See also: [README.md](README.md) | [Commands](commands/index.md) | [Templates](templates/index.md)*
