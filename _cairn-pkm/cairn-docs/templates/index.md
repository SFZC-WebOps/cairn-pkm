# Templates

Templater templates for creating files with consistent structure.

---

## Overview

Templates use [Templater](https://github.com/SilentVoid13/Templater) syntax to prompt for input and generate structured files.

**Location:** `_cairn-pkm/templates/`

---

## Available Templates

| Template | Purpose | Output Location |
|----------|---------|-----------------|
| [capture-quick](capture-quick.md) | Quick inbox capture | `Capture/` |
| [task](task.md) | New task file | `Tracks/*/tasks/` |
| [track-project](track-project.md) | New project | `Tracks/p###-xxxx-yyyy/` |
| [track-area](track-area.md) | New area | `Tracks/area-xxx/` |
| [object](object.md) | Universal object | `Objects/` |
| [object-financial](object-financial.md) | Financial object | `Objects/` or track |
| [review-weekly](review-weekly.md) | Weekly review | Varies |

---

## Quick Reference

### capture-quick

Creates a timestamped inbox item.

**Prompts:**
- Subject (what's this about?)

**Creates:**
- `Capture/YYYYMMDD-HHMMSS-slug.md`

---

### task

Creates a task file in the current or specified track.

**Prompts:**
- Task title
- Track (auto-detected or prompted)
- Priority (low, medium, high, critical)
- Status (active, blocked, waiting, scheduled, deferred)
- Due date (optional)

**Creates:**
- `Tracks/{track}/tasks/YYYYMMDD-slug.md`

---

### track-project

Creates a complete project folder structure.

**Prompts:**
- Project name
- System code (4 letters)
- Action code (4 letters)

**Creates:**
```
Tracks/p###-xxxx-yyyy/
├── _p###-xxxx-yyyy-home.md
├── resources/
├── tasks/
└── zzz/
```

---

### track-area

Creates a complete area folder structure.

**Prompts:**
- Area name

**Creates:**
```
Tracks/area-xxx/
├── _area-xxx-home.md
├── resources/
├── tasks/
└── zzz/
```

---

### object

Creates an object file with type-specific fields.

**Prompts:**
- Object type (contact, vendor, tool, account, subscription, device, location)
- Type-specific details

**Creates:**
- `Objects/{type}-{slug}.md`

---

### object-financial

Creates a financial object (account, credit card, loan, etc.)

**Prompts:**
- Account/card name
- Financial type
- Provider
- Payment details
- Contact info

**Creates:**
- `Objects/{type}-{slug}.md` (or track resources)

---

### review-weekly

Creates a weekly review document with Dataview queries.

**Creates:**
- `review-YYYY-WNN.md`

---

## Using Templates

### In Obsidian

1. Create new file
2. Use Templater command palette or hotkey
3. Select template
4. Follow prompts
5. File is renamed and moved automatically

### Template Configuration

In Templater settings:
- Template folder: `_cairn-pkm/templates`
- Enable "Trigger on new file creation"

---

## Custom Templates

Add vault-specific templates to `_local/templates/`.

They won't be affected by Cairn-PKM updates.

---

*Back to [README](../README.md)*
