# CAIRN

A markdown-based personal knowledge management system for Obsidian.

## What is CAIRN?

CAIRN is an opinionated folder structure for organizing personal and professional knowledge in plain markdown files. Like the stone trail markers that guide hikers through unfamiliar terrain, it provides just enough structure to keep you oriented without getting in your way.

## Features

- **Plain text** — Markdown files with YAML frontmatter. No lock-in.
- **Co-location** — Everything for a project lives in its folder.
- **Portable core** — The system (`_CAIRN/`) can be shared across vaults as a git submodule.
- **Local customization** — Vault-specific stuff stays in `_local/`, never shared.
- **Two track types** — Areas (ongoing) and Projects (temporary). Nothing in between.

## Structure

```
{Vault}/
├── _CAIRN/      # The system (this repo)
├── _local/      # Your customizations
├── Capture/     # Inbox
├── Objects/     # Universal entities (people, tools, accounts)
└── Tracks/      # Areas and projects
```

## What's in _CAIRN/

```
_CAIRN/
├── LICENSE
├── README.md
├── llm/
│   ├── ARCHITECTURE.md   # Full system documentation
│   ├── INSTALLATION.md   # Setup guide
│   └── commands/         # LLM command specifications
├── templates/            # Templater templates
├── tools/                # Tool definitions
└── views/                # Dataview dashboards
```

## Quick Start

1. Clone or copy this into your Obsidian vault as `_CAIRN/`
2. Create the other folders: `_local/`, `Capture/`, `Objects/`, `Tracks/`
3. Create your first area: `Tracks/area-personal/`
4. Read [INSTALLATION.md](llm/INSTALLATION.md) for detailed setup instructions

### As a git submodule

```bash
git submodule add https://github.com/yourname/cairn-pkm.git _CAIRN
```

## Documentation

- **[INSTALLATION.md](llm/INSTALLATION.md)** — Setup and migration guide
- **[ARCHITECTURE.md](llm/ARCHITECTURE.md)** — Complete system documentation
- **[llm/commands/](llm/commands/)** — LLM command specifications for AI-assisted workflows

## Tools Included

### Changelog
Track production changes with structured YAML entries. See `tools/changelog/README.md`.

### Skill Tracker
Document professional skills using the SFIA framework. See `tools/skill-tracker/`.

## Requirements

- [Obsidian](https://obsidian.md) (recommended)
- [Templater](https://github.com/SilentVoid13/Templater) plugin (for templates)
- [Dataview](https://github.com/blacksmithgu/obsidian-dataview) plugin (for views)

Or any text editor — it's just markdown files.

## License

MIT License. See [LICENSE](LICENSE).
