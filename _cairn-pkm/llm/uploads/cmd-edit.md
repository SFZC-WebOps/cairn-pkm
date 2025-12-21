# !edit - Unified Edit Command
*Type: Read/Write | Updated: 2025-12-20*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!edit [filename]` | Edit file by name (reads from vault) |
| `!edit` | Edit uploaded file (auto-detects type) |

**Workflow:**
- **By filename:** `!edit 20251220-ssl-fix.md` → Interactive loop → Confirm → Output
- **By upload:** Upload file → `!edit` → Interactive loop → Confirm → Output

---

## Initialization

Per `cmd-shared-patterns.md`

---

## File Resolution

```
IF user provides filename:
  SEARCH vault for matching file:
    1. Check {VAULT_PATH}/Tracks/*/tasks/{filename}
    2. Check {VAULT_PATH}/Tracks/*/_*-home.md matching pattern
    3. Check {VAULT_PATH}/Objects/{filename}
    4. Check {VAULT_PATH}/Tracks/*/resources/{filename}
  
  IF found: READ file
  IF not found: "File '{filename}' not found. Check name or upload file."
  IF multiple matches: "Found multiple matches:\n{list}\nSpecify full path or upload specific file."

ELSE IF user uploaded file:
  READ: /mnt/user-data/uploads/{uploaded_file}

ELSE:
  PROMPT: "Provide filename or upload file to edit"
```

---

## Type Detection

```
READ: uploaded file
DETECT entity type:

1. Check frontmatter "type" field
   - type: area → EDIT_AREA
   - type: project → EDIT_PROJECT
   - type: task → EDIT_TASK
   - type: {object-type} → EDIT_OBJECT

2. Check filename pattern
   - _area-*-home.md → EDIT_AREA
   - _p###-*-home.md → EDIT_PROJECT
   - YYYYMMDD-*.md in tasks/ → EDIT_TASK
   - contact-*.md, device-*.md, etc. → EDIT_OBJECT

3. Check file location
   - Tracks/area-*/ → EDIT_AREA
   - Tracks/p###-*/ → EDIT_PROJECT
   - */tasks/ → EDIT_TASK
   - Objects/ → EDIT_OBJECT

IF unable to detect: "Could not determine file type. Is this an area, project, task, or object?"
```

---

## Edit Areas

### Supported Operations

| Operation | Command |
|-----------|---------|
| Add log entry | `log [type] [text]` |
| Update status | `status [value]` |
| Update description | `desc [text]` |
| Edit any field | `field [name] [value]` |

### Log Entry Types

decision, update, milestone, issue, note

### Example Session (by filename)

```
> !edit _area-finance-home.md
Reading area-finance-home.md...
Editing area-finance...

What changes?

> log decision Switching to quarterly review cycle

Added log entry. Continue editing or type 'done'.

> status active

Updated status. Continue editing or type 'done'.

> done

[Output file per preferences]
```

### Example Session (by upload)

```
> Uploaded: _area-finance-home.md
> !edit
Reading uploaded file...
Editing area-finance...

What changes?

> log decision Switching to quarterly review cycle

Added log entry. Continue editing or type 'done'.

> done

[Output file per preferences]
```

### Updates Applied

```
UPDATE: frontmatter modified date
APPEND: log entry to Log section
  {YYYY-MM-DD HH:MM} - {Type} - {Text}
```

**Output:** Per `cmd-output-behavior.md`

---

## Edit Projects

### Supported Operations

| Operation | Command |
|-----------|---------|
| Add log entry | `log [type] [text]` |
| Update status | `status [value]` |
| Update progress | `progress [0-100]` |
| Update both | `update status [value] progress [number]` |
| Edit any field | `field [name] [value]` |

### Status Values

active, onhold, blocked, complete, archived

### Example Session

```
> !edit _p014-blog-migr-home.md
Reading p014-blog-migr-home.md...
Editing p014-blog-migr...

What changes?

> progress 75

Updated progress. Continue editing or type 'done'.

> log milestone Content migration complete

Added log entry. Continue editing or type 'done'.

> done

[Output file per preferences]
```

### Updates Applied

```
UPDATE: frontmatter modified date
UPDATE: frontmatter progress (if changed)
UPDATE: frontmatter status (if changed)
APPEND: log entry to Log section
  {YYYY-MM-DD HH:MM} - {Type} - {Text}
```

**Output:** Per `cmd-output-behavior.md`

---

## Edit Tasks

### Supported Operations

| Operation | Command |
|-----------|---------|
| Add history entry | `history [text]` |
| Update status | `status [value]` |
| Update priority | `priority [value]` |
| Update phase | `phase [value]` |
| Update effort | `effort [value]` |
| Update viz | `viz [value]` |
| Set due date | `due [YYYY-MM-DD]` |
| Add type | `type add [type]` |
| Remove type | `type remove [type]` |
| Add subtask | `subtask [text]` |
| Edit any field | `field [name] [value]` |

### Interactive Commands

```
history                - Add task history entry (auto-generated from conversation)
history [text]         - Add specific history entry
status [value]         - active | blocked | complete | deferred | onhold | scheduled | waiting
priority [value]       - low | medium | high | critical
phase [value]          - planning | executing | testing | closing
effort [value]         - simple | moderate | complex
viz [value]            - now | next | soon | later | blocked | waiting
due [YYYY-MM-DD]       - Set due date
due clear              - Remove due date
type add [type]        - Add type tag
type remove [type]     - Remove type tag
subtask [text]         - Add checkbox to Subtasks section
field [name] [value]   - Update any frontmatter field
done                   - Finalize and output
```

### Example Session

```
> !edit 20251220-ssl-cert-fix.md
Reading 20251220-ssl-cert-fix.md from p014-blog-migr/tasks/...
Editing task: Fix SSL certificate chain validation...

What changes?

> status complete

Updated status. Continue editing or type 'done'.

> history Verified certificate chain, deployed to production

Added history entry. Continue editing or type 'done'.

> done

[Output file per preferences]
```

### Auto-Generated History Entry

```
SCAN conversation for:
- Action taken
- Outcome
- Decisions made

FORMAT: "YYYY-MM-DD: {action} - {outcome}"

Example: "2025-12-20: Approved configuration change - implementation scheduled"
```

### Updates Applied

```
PREPEND: history entry to Task History section
  - YYYY-MM-DD: {entry}
UPDATE: last_update in frontmatter (if present)
UPDATE: frontmatter fields (status, priority, etc.)
APPEND: subtasks to Subtasks section (if added)
```

**Output:** Per `cmd-output-behavior.md`

---

## Edit Objects

### Supported Operations

| Operation | Command |
|-----------|---------|
| Update status | `status [value]` |
| Add note | `note [text]` |
| Update field | `field [name] [value]` |
| Add alias | `alias [text]` |
| Add tag | `tag [text]` |

### Status Values

active, inactive, archived

### Example Session

```
> !edit contact-hazel-frost.md
Reading contact-hazel-frost.md from Objects/...
Editing contact-hazel-frost...

What changes?

> field email hazel.frost@example.com

Updated email. Continue editing or type 'done'.

> note Met at conference 2025-12-20

Added note. Continue editing or type 'done'.

> done

[Output file per preferences]
```

### Updates Applied

```
UPDATE: frontmatter lastmod date
UPDATE: frontmatter fields (as modified)
APPEND: notes to Notes section (if added)
```

**Output:** Per `cmd-output-behavior.md`

---

## Common Edit Pattern

All entity types follow this flow:

```
1. Display current date/time
2. Read uploaded file
3. Detect entity type
4. Show current state (relevant fields)
5. Prompt: "What changes?"
6. Apply changes
7. Show updated state
8. Loop until 'done'
9. Output per file_operations setting
```

---

## Smart Inference

### From Conversation

```
SCAN for:
- Status changes: "mark complete", "block this", "waiting on X"
- Priority changes: "this is urgent", "low priority now"
- Updates: "progress is at 80%", "status should be onhold"

SUGGEST edit commands before prompting user
```

### History Entry Generation

For tasks, if user says "history" without text:

```
ANALYZE conversation:
- What was accomplished
- What changed
- What was decided

FORMAT: One line, <200 chars, past tense, outcome-focused
SHOW: "Suggested history: {entry}"
PROMPT: "Use this or provide your own?"
```

---

## Interactive Commands Reference

| Command | All Types | Areas/Projects | Tasks | Objects |
|---------|-----------|----------------|-------|---------|
| `done` | ✓ | ✓ | ✓ | ✓ |
| `status [value]` | ✓ | ✓ | ✓ | ✓ |
| `field [name] [value]` | ✓ | ✓ | ✓ | ✓ |
| `log [type] [text]` | — | ✓ | — | — |
| `progress [0-100]` | — | ✓ (projects only) | — | — |
| `history [text]` | — | — | ✓ | — |
| `priority [value]` | — | — | ✓ | — |
| `phase [value]` | — | — | ✓ | — |
| `effort [value]` | — | — | ✓ | — |
| `viz [value]` | — | — | ✓ | — |
| `due [date]` | — | — | ✓ | — |
| `type add/remove` | — | — | ✓ | — |
| `subtask [text]` | — | — | ✓ | — |
| `note [text]` | — | — | — | ✓ |
| `alias [text]` | — | — | — | ✓ |
| `tag [text]` | — | — | — | ✓ |

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No filename or upload | "Provide filename or upload file to edit" |
| File not found (by name) | "File '{filename}' not found. Check name or upload file." |
| Multiple matches | "Found multiple matches:\n{list}\nSpecify full path or upload specific file." |
| Cannot detect type | "Could not determine file type. Is this an area, project, task, or object?" |
| Invalid command | "Unknown command. Type 'help' for available commands" |
| Invalid value | "Invalid {field} value. Options: {valid_values}" |
| File parse error | "Could not parse file. Check YAML frontmatter format" |

Common errors: See `cmd-shared-patterns.md`

---

## Help Command

During edit loop, user can type `help`:

```
Available commands for {entity_type}:
{list of applicable commands from table above}

Type 'done' when finished.
```

---

## Integration

| Works With | How |
|------------|-----|
| !create | Edit entities created via !create |
| !hi | Can edit track home docs |
| !bye | Logs edits in session summary |
