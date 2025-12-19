# Tools

Specialized workflow tools in Cairn-PKM.

---

## Overview

Tools are systems for specific workflows. Each tool has:
- **Definition** in `_cairn-pkm/tools/` (how it works)
- **Data output** in `_local/data/` (your generated content)

---

## Available Tools

| Tool | Purpose | Command |
|------|---------|---------|
| [Changelog](changelog.md) | Track production changes | `!change` |
| [Skill Tracker](skill-tracker.md) | Professional development | `!sk` |

---

## Tool Structure

```
_cairn-pkm/tools/
├── changelog/
│   ├── README.md
│   └── templates/
└── skill-tracker/
    ├── detection-patterns.md
    ├── skill-evidence.md
    ├── skill-matrix.md
    └── skills-registry.md

_local/data/
├── changelog/
│   └── YYYY/
│       └── MM/
│           └── CHG-*.yaml
└── skill-tracker/
    └── skill-evidence.md
```

---

## Tool Design Principles

1. **Definitions portable** — Tool specs stay in `_cairn-pkm/`
2. **Data stays local** — Output goes to `_local/data/`
3. **LLM-assisted** — Commands extract and structure information
4. **Plain text** — YAML, Markdown formats

---

*Back to [README](../README.md)*
