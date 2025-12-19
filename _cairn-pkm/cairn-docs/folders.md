# Folder Structure

The five top-level domains and their contents.

---

## Overview

```
{Vault}/
├── _cairn-pkm/      # System (portable)
├── _local/          # Your customizations
├── Capture/         # Inbox
├── Objects/         # Universal entities
└── Tracks/          # Areas and projects
```

---

## _cairn-pkm/

**Purpose:** The portable system core.

**Contents:**
- `llm/commands/` — LLM command specifications
- `templates/` — Templater templates
- `tools/` — Tool definitions
- `views/` — Dataview dashboards
- `README.md`, `ARCHITECTURE.md`, `INSTALLATION.md`
- `VERSION` — Current version info

**Portability:** Can be updated by replacing the folder. Your content stays untouched.

See: [Commands](commands/index.md) | [Templates](templates/index.md) | [Tools](tools/index.md) | [Views](views/index.md)

---

## _local/

**Purpose:** Vault-specific customizations and tool output.

**Structure:**
```
_local/
├── data/           # Tool output
│   ├── changelog/  # Change entries
│   └── skill-tracker/
├── templates/      # Custom templates
├── tools/          # Custom tool configs
└── views/          # Custom dashboards
```

**Key principle:** Definitions in `_cairn-pkm/`, data in `_local/`.

**Never shared:** This folder is vault-specific and excluded from distribution.

---

## Capture/

**Purpose:** Inbox for unprocessed items.

**What goes here:**
- Quick notes
- Screenshots
- Ideas
- Anything not yet processed

**Processing:** During review, each item either:
- Becomes a **task** → `Tracks/*/tasks/`
- Becomes **reference** → `Objects/` or `Tracks/*/resources/`
- Gets **deleted**

**Goal:** Process to zero regularly.

See: [capture-quick template](templates/capture-quick.md) | [capture-inbox view](views/capture-inbox.md)

---

## Objects/

**Purpose:** Universal entities that cross project boundaries.

**What goes here:**
- Contacts (people)
- Vendors (service providers)
- Tools (software)
- Accounts (credentials)
- Subscriptions (recurring services)
- Devices (hardware)
- Locations (places)

**Naming:** `{type}-{identifier}.md`
- `contact-jane-smith.md`
- `vendor-acme-hosting.md`
- `tool-obsidian.md`

**When to use Objects vs. track resources:**

| Objects/ | Track resources/ |
|----------|------------------|
| Referenced by multiple tracks | Only relevant to one track |
| Exists independent of projects | Created for this project |
| Long-lived | Project-specific |

See: [object template](templates/object.md) | [!obj command](commands/cmd-obj.md)

---

## Tracks/

**Purpose:** Where work happens — areas and projects.

**Structure:**
```
Tracks/
├── area-{domain}/        # Ongoing areas
│   ├── _area-{domain}-home.md
│   ├── resources/
│   ├── tasks/
│   └── zzz/
└── p###-xxxx-yyyy/       # Time-bound projects
    ├── _p###-xxxx-yyyy-home.md
    ├── resources/
    ├── tasks/
    └── zzz/
```

### Areas vs. Projects

| Aspect | Areas | Projects |
|--------|-------|----------|
| Duration | Ongoing, no end | Temporary, has finish line |
| Naming | `area-{domain}` | `p###-xxxx-yyyy` |
| Examples | `area-finance`, `area-health` | `p042-blog-migr` |
| When done | Never (archive if life changes) | Archive to `zzz/` or delete |

See: [Home Docs](home-docs.md) | [Tasks](tasks.md) | [track-project template](templates/track-project.md) | [track-area template](templates/track-area.md)

---

*Back to [README](README.md)*
