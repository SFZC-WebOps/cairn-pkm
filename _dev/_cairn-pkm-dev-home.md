---
title: Cairn-PKM Development
type: area
status: active
created: 2024-12-18
modified: 2024-12-18
---

## Overview

Development area for the Cairn-PKM system. This area tracks work on the core system, releases, and ongoing maintenance. Everything in `_dev/` is excluded from user releases.

## Current Focus

- Preparing v1.0.0 initial release
- Creating example content for distribution
- Documenting development workflow
- Testing command specifications

## Resources

- [[WORKFLOW|Development & Release Workflow]]
- [[README|_dev folder guide]]
- GitHub: https://github.com/SFZC-WebOps/cairn-pkm

## Notes

**Branch strategy:**
- `dev` - Daily work (Obsidian Git auto-commits)
- `stable` - Release versions (manual merges)
- `main` - Initial setup (not actively used)

**What's distributed to users:**
- `_CAIRN/` - Core system (commands, templates, views)
- `Tracks/`, `Objects/`, `Capture/` - Example content
- Root docs - SETUP-GUIDE.md

**What stays private (in `_dev/`):**
- This home doc
- WORKFLOW.md
- Development notes
- Design decisions

---

## Log

---

2024-12-18 15:21 - Development - Completed CAIRN-dev vault setup and Git infrastructure
Created complete development environment with example content, cross-linked structure, and automated Git workflow.
- Files: Created 64 files including areas, projects, tasks, objects, captures, commands, and documentation
- Configured: Obsidian Git plugin for auto-backup, dev/stable branch workflow, release process
- Documentation: Added WORKFLOW.md in _dev/ folder with complete development and release procedures
- AI Contribution: Assistant designed vault structure, created realistic office examples, implemented cross-linking system, and documented complete Git workflow

---

2024-12-18 14:30 - Configuration - Git repository initialized and connected to GitHub
Set up cairn-pkm repository with dev/stable branches, configured Obsidian Git for auto-backup.
- Repository: https://github.com/SFZC-WebOps/cairn-pkm
- Branches: main, dev (active), stable (releases)
- Auto-commit: Every 5 minutes to dev branch

---

2024-12-18 13:00 - Development - Created CAIRN-dev vault structure
Built complete folder structure with example content for office administration scenarios.
- Structure: _CAIRN/, _local/, _dev/, Capture/, Objects/, Tracks/
- Examples: area-admin, p001-office-move, 7 objects, 5 captures, 6 tasks
- Documentation: VIZ-VALUES.md, SETUP-GUIDE.md
- AI Contribution: Assistant created realistic office examples with cross-linking

---

2024-12-18 - Configuration - Created _dev area home doc
Added development area tracking with home doc for CAIRN system work.
