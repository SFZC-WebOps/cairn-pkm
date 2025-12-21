# !create - Unified Creation Command
*Type: Write | Updated: 2025-12-20*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!create area` | Create new area |
| `!create project` | Create new project |
| `!create task` | Create new task |
| `!create object [type]` | Create new object |

**Workflow:** Specify type → Interactive prompts → Review draft → Confirm → Output

---

## Initialization

Per `cmd-shared-patterns.md`

---

## Entity Type Detection

```
PARSE: user input
MATCH:
  - "area" | "area-*" → CREATE_AREA
  - "project" | "p###-*" → CREATE_PROJECT
  - "task" → CREATE_TASK
  - "object [type]" | "[object-type]" → CREATE_OBJECT
  
IF ambiguous: Ask "Create what? (area | project | task | object [type])"
```

---

## !create area

### Prompts

```
Creating new area...

1. Domain name? (becomes area-{domain})
   Examples: finance, health, webops, personal
   
2. Brief description? (1-2 sentences)
```

### Generation

```
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/area-{domain}/
CONSTRUCT: home_doc = {folder_path}_area-{domain}-home.md
CREATE: subfolders resources/, tasks/, zzz/
```

### Template

```yaml
---
title: {user input}
type: area
status: active
created: {YYYY-MM-DD}
modified: {YYYY-MM-DD}
---

## Overview
{user description}

## Current Focus

## Notes

---

## Log

---

{YYYY-MM-DD HH:MM} - Setup - Area created

---
```

**Output:** Per `cmd-output-behavior.md`

---

## !create project

### Prompts

```
Creating new project...

1. System code? (4 letters) - blog, home, work, sfzc
2. Action code? (4 letters) - migr, setup, audt, docs
3. Project title?
4. Brief description? (1-2 sentences)
```

### Generation

```
SCAN: {VAULT_PATH}/Tracks/ for existing p###-* folders
CALCULATE: next_number = highest + 1, zero-padded to 3 digits
CONSTRUCT: project_id = p{next_number}-{system}-{action}
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/{project_id}/
CONSTRUCT: home_doc = {folder_path}_{project_id}-home.md
CREATE: subfolders resources/, tasks/, zzz/
```

### Template

```yaml
---
project_id: {project_id}
title: {user input}
type: project
status: active
progress: 0
created: {YYYY-MM-DD}
modified: {YYYY-MM-DD}
---

## Overview
{user description}

## Current Focus

## Notes

---

## Log

---

{YYYY-MM-DD HH:MM} - Setup - Project created

---
```

**Output:** Per `cmd-output-behavior.md`

---

## !create task

### Execution

1. Display current date/time
2. Infer track context (from !hi, conversation, or ask user)
3. Extract from conversation: title, types, mentioned fields
4. Generate filename: `{YYYYMMDD}-{slug}.md`
5. Show draft with inferred values
6. Interactive edit loop until `done`
7. Output per file_operations setting

### Required Fields

| Field | Default | Notes |
|-------|---------|-------|
| title | (from conversation) | Must be provided |
| project | (from context) | Track identifier |
| priority | medium | low \| medium \| high \| critical |
| status | active | active \| blocked \| complete \| deferred \| onhold \| scheduled \| waiting |

### Optional Fields

due_date, assignee, phase, effort, viz, type[], parent_task

### Interactive Commands

```
done                 - Finalize and output
edit [field]         - Modify any field
status [value]       - Change status
priority [value]     - Change priority
phase [value]        - planning | executing | testing | closing
effort [value]       - simple | moderate | complex
viz [value]          - now | next | soon | later | blocked | waiting
type add [type]      - Add type tag
type remove [type]   - Remove type tag
```

### Template

```yaml
---
title: "{title}"
project: "{track}"
created_date: YYYY-MM-DD
due_date: 
assignee: {default_assignee}
parent_task: 
priority: {priority}
status: {status}
phase: {phase}
effort: {effort}
viz: 
type:
  - {types}
---

### Task History
- YYYY-MM-DD: Created task - {summary}

---

### Subtasks
- [ ] {subtask}

---

### Notes

---
```

**Output:** Per `cmd-output-behavior.md`

---

## !create object

### Syntax

`!create object [type]` or `!create [object-type]`

Examples:
- `!create object contact`
- `!create credit-card`
- `!create device`

### Supported Types

| Type | Filename Pattern | Example |
|------|------------------|---------|
| contact | `contact-{firstname-lastname}.md` | `contact-hazel-frost.md` |
| credit-card | `credit-card-{issuer}-{name}.md` | `credit-card-chase-sapphire.md` |
| account | `account-{institution}-{type}.md` | `account-chase-checking.md` |
| loan | `loan-{lender}.md` | `loan-lendingclub.md` |
| utility | `utility-{provider}.md` | `utility-pge.md` |
| telecom | `telecom-{provider}.md` | `telecom-google-fi.md` |
| subscription | `subscription-{service}.md` | `subscription-netflix.md` |
| device | `device-{type}-{identifier}.md` | `device-laptop-thinkpad.md` |
| medication | `medication-{name}.md` | `medication-lisinopril.md` |
| provider | `provider-{name}.md` | `provider-dr-smith.md` |

### Execution

1. Identify object type
2. Apply type-specific template
3. Populate fields from conversation/prompts
4. Use `TBD` for missing required fields
5. Interactive edit loop until `done`
6. Output per file_operations setting

### Destination

| Scope | Location |
|-------|----------|
| Cross-cutting (multiple tracks) | `{VAULT_PATH}/Objects/` |
| Track-specific | `{VAULT_PATH}/Tracks/{track}/resources/` |

Ask user if unclear: "Is this for multiple tracks (Objects/) or specific to one track?"

### Base Template

```yaml
---
type: {object-type}
status: active
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
aliases: []
tags: []
# Type-specific fields below
---

## Overview

## Details

## Notes

---
```

Type-specific fields added based on object type.

**Output:** Per `cmd-output-behavior.md`

---

## Slug Generation

For filenames that need slugs (tasks, some objects):

**Rules:**
- 3-4 key words from title/name
- Lowercase, hyphenated
- Max 40 characters
- Remove articles (a, an, the)

| Input | Slug |
|-------|------|
| "Fix SSL certificate chain validation" | `ssl-cert-chain-validation` |
| "John Q. Smith" | `john-q-smith` |

---

## Inference Patterns

### Track Context

```
CHECK in order:
1. Active !hi session context
2. Conversation mentions ("for p14", "in area-finance")
3. Most recently discussed track
4. Ask user: "Which track is this for?"
```

### Priority

| Phrase | Priority |
|--------|----------|
| urgent, critical, emergency, ASAP | critical |
| important, high priority, soon | high |
| normal, standard | medium |
| nice to have, low priority, eventual | low |

### Status

| Phrase | Status |
|--------|--------|
| working on, in progress, doing | active |
| stuck, blocked by, can't proceed | blocked |
| waiting for, pending, expecting | waiting |
| on hold, paused, deferred | onhold |

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Unknown entity type | List valid types, ask to clarify |
| Missing required field | Populate with TBD or ask user |
| Track not found | List available tracks |
| Conflicting information | Ask user to clarify |
| Folder already exists (area/project) | "Already exists. Use !edit instead?" |

Common errors: See `cmd-shared-patterns.md`

---

## Integration

| Works With | How |
|------------|-----|
| !hi | Uses session context for track inference |
| !edit | Created entities can be edited |
| !bye | Logs creation in session summary |
