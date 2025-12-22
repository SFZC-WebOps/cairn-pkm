# !help - Command Reference
*Type: Display | Updated: 2025-12-22*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!help` | List all available commands |
| `!help [cmd]` | Show specific command usage |
| `help` (in edit loop) | Show context-specific options |

---

## !help "" List Commands

```
SCAN: /mnt/project/cmd-*.md files
EXCLUDE: cmd-shared-patterns.md, cmd-output-behavior.md (include files, not commands)

FOR EACH cmd-*.md:
  EXTRACT: command name from filename (cmd-{name}.md → !{name})
  EXTRACT: description from title line (# !cmd - {Description})

OUTPUT:
  Available Commands:
  
  !{name}    {Description}
  !{name}    {Description}
  ...
  
  Type !help [command] for details
```

---

## !help [cmd] "" Specific Command

Extract and display from target cmd-*.md:

1. **Quick Reference table** "" command variants and what they do
2. **Basic workflow** "" one-line description from file header
3. **Interactive commands** "" if applicable (edit loops)

**Example output for `!help edit`:**

```
!edit - Unified Edit Command

| Command | Purpose |
|---------|---------|
| !edit [filename] | Edit file by name |
| !edit | Edit uploaded file |

Workflow: Provide filename or upload → Interactive loop → Confirm → Output

Interactive commands: status, priority, phase, effort, viz, due, history, 
                      log, progress, field, subtask, done, help
```

---

## Discovery Pattern

Help hints appear in these locations:

| Location | Format |
|----------|--------|
| !hi menu footer | `Type !help for command reference` |
| Edit loop first prompt | `What changes? (help for options, done to finish)` |
| Unknown command error | `Unknown command. Try !help` |
| Invalid input error | `Invalid [field]. Type help for options.` |

---

## In-Context Help

During interactive edit loops, `help` shows only commands valid for current entity type.

**Example:** User editing a task, types `help`:

```
Task edit commands:
  status [value]     active | blocked | complete | deferred | onhold | scheduled | waiting
  priority [value]   low | medium | high | critical
  phase [value]      planning | executing | testing | closing
  effort [value]     simple | moderate | complex
  viz [value]        now | next | soon | later | blocked | waiting
  due [YYYY-MM-DD]   Set due date (or 'due clear')
  history [text]     Add task history entry
  subtask [text]     Add subtask checkbox
  field [name] [val] Update any frontmatter field
  done               Finalize and output
```

See cmd-edit.md for entity-specific command tables.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| `!help xyz` (unknown) | "Unknown command 'xyz'. Type !help for available commands." |
| `help` outside edit loop | Treat as `!help` |
