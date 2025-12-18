# _dev/ Folder

**Development-only files - excluded from releases**

This folder contains notes, documentation, and resources for maintaining the CAIRN system. Contents are **not distributed** to end users.

## Purpose

- Development workflow documentation
- Release checklists
- Design notes and decisions
- Testing procedures
- Contributor guidelines

## Structure

```
_dev/
├── README.md          # This file
├── WORKFLOW.md        # Development & release workflow
├── notes/             # Development notes
└── archive/           # Old versions, deprecated docs
```

## What Belongs Here

**Include:**
- How to develop and maintain CAIRN
- Release procedures
- Git workflow guides
- Design decisions and rationale
- Testing checklists
- Contributor documentation

**Don't Include:**
- End user documentation (goes in `_CAIRN/`)
- Example content (goes in main vault)
- System files (those go in `_CAIRN/`)

---

When creating release zips, this folder is **excluded** via the zip command in WORKFLOW.md.
