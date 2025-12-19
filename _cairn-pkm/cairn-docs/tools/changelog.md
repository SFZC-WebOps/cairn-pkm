# Changelog Tool

Track production changes with structured YAML entries.

---

## Purpose

Document changes with:
- What changed and why
- Systems and technologies involved
- How to roll it back
- AI contribution (if applicable)
- Searchable history

---

## File Locations

| Purpose | Path |
|---------|------|
| Tool definition | `_cairn-pkm/tools/changelog/README.md` |
| Schema template | `_cairn-pkm/tools/changelog/templates/change-template.yaml` |
| Your data | `_local/data/changelog/YYYY/MM/CHG-*.yaml` |

---

## Usage

Use the `!change` command after making a production change:

```
!change
```

The command:
1. Analyzes your conversation
2. Extracts systems, technologies, problem, solution
3. Generates a draft YAML entry
4. Lets you edit fields
5. Saves to `_local/data/changelog/YYYY/MM/`

See [!change command](../commands/cmd-change.md) for detailed usage.

---

## File Naming

**Format:** `CHG-YYYYMMDD-HHMMSS.yaml`

**Example:** `CHG-20251105-140530.yaml`

---

## Schema Overview

### Required Fields

| Field | Description |
|-------|-------------|
| `change_id` | Unique identifier (auto-generated) |
| `timestamp` | When change was made |
| `implemented_by` | Who made the change |
| `title` | One-line summary (< 80 chars) |
| `systems_affected` | List of systems changed |
| `technologies` | Technologies involved |
| `problem_category` | security, performance, bug, feature, configuration |
| `change_classification` | Impact, risk, reversibility, scope |
| `description` | Brief context |
| `problem` | What was broken |
| `solution` | What was done |

### Optional Fields

| Field | Description |
|-------|-------------|
| `files_modified` | Changed files |
| `rollback` | How to undo |
| `validation` | Testing performed |
| `ai_contribution` | AI role and time saved |
| `related_changes` | Links to related entries |
| `project_context` | Associated project ID |

---

## Categories

| Category | Use for |
|----------|---------|
| security | SSL, auth, vulnerabilities, patches |
| performance | Speed, optimization, resources |
| bug | Something broken |
| feature | New capability |
| configuration | Settings, deployment, setup |

---

## Impact Levels

| Level | Description |
|-------|-------------|
| low | Minor, single component |
| medium | Multiple components, visible |
| high | Critical systems, major impact |
| critical | Emergency, production down |

---

## AI Roles

| Role | Description |
|------|-------------|
| advisor | Suggested approach |
| executor | Wrote working code/config |
| validator | Verified solution |
| researcher | Found documentation |

---

## Best Practices

**DO document:**
- Production changes
- Configuration changes
- Security updates
- Bug fixes
- Emergency repairs

**DON'T document:**
- Development work (pre-production)
- Planning sessions
- Meetings without changes

**Good titles:**
- ✅ "Fixed Redis cache configuration causing slow homepage"
- ❌ "Redis issue"

---

## Searching Changes

```bash
# By technology
grep -r "ssl" _local/data/changelog/

# By system
grep -l "example.com" _local/data/changelog/2025/*/*.yaml

# By impact
grep -l "impact: high" _local/data/changelog/2025/*/*.yaml
```

---

*Back to [Tools Index](index.md) | See also: [!change command](../commands/cmd-change.md)*
