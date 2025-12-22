# !create - Unified Creation Command
*Type: Write | Updated: 2025-12-22*

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

### Title Generation

Title is auto-generated from domain name:
- Capitalizes first letter of domain
- If description starts with capitalized phrase, uses that
- Otherwise uses "{Domain} Management" pattern

**Examples:**
- Domain: "finance" + Description: "Personal finance tracking" → Title: "Personal Finance"
- Domain: "facilities" + Description: "Office space management" → Title: "Facilities Management"
- Domain: "webops" + Description: "Web operations and infrastructure" → Title: "Web Operations"

### Generation

```
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/area-{domain}/
CONSTRUCT: home_doc = {folder_path}_area-{domain}-home.md

CREATE subfolders (CRITICAL - create separately to avoid shell expansion issues):
  mkdir -p {folder_path}resources
  mkdir -p {folder_path}tasks
  mkdir -p {folder_path}zzz
```

### Confirmation Preview

```
**Proposed Area:**
- Name: area-{domain}
- Title: {title}
- Folder: {VAULT_PATH}/Tracks/area-{domain}/
- Subfolders: resources/, tasks/, zzz/

Review the content below before confirming:

[Full YAML shown below for reference]
```

### Template

```yaml
---
title: {generated_title}
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

1. System code? (3-5 letters) - What system/area is affected?
   Examples: blog, home, work, sfzc, infra, site

2. Action code? (3-5 letters) - What action is being taken?
   Examples: migr (migration), setup, audt (audit), docs, upgr (upgrade)

3. Project title?

4. Brief description? (1-2 sentences)
```

### Project Naming Best Practices

**Understanding System vs Action Codes:**

Codes are flexible and context-dependent. A code can function as either system or action depending on your project:

**Typically System Codes** (what's being changed):
- blog - Blog/website
- infra - Infrastructure  
- home - Home/personal systems
- work - Work systems
- site - Website/web property
- facl - Facilities
- hvac - HVAC system

**Typically Action Codes** (what you're doing):
- migr - Migration
- upgr - Upgrade
- setup - Initial setup
- audt - Audit/review
- docs - Documentation
- reno - Renovation
- impl - Implementation

**Context matters:** The code "hvac" could be:
- A system code in "p003-hvac-upgr" (upgrading the HVAC system)
- An action code in "p003-facl-hvac" (doing HVAC work on facilities)

Choose codes that make sense for your project context.

**Good examples:**
- p001-blog-migr (migrate blog platform)
- p002-infra-upgr (upgrade infrastructure)
- p003-home-reno (home renovation)
- p004-site-setup (new site setup)
- p005-facl-hvac (HVAC work on facilities)
- p006-hvac-upgr (upgrading HVAC system)

**Avoid:**
- p001-fix-stuff (too vague)
- p002-blog-migration (action code too long)

### Generation

```
SCAN: {VAULT_PATH}/Tracks/ for existing p###-* folders
CALCULATE: next_number = highest + 1, zero-padded to 3 digits
CONSTRUCT: project_id = p{next_number}-{system}-{action}
CONSTRUCT: folder_path = {VAULT_PATH}/Tracks/{project_id}/
CONSTRUCT: home_doc = {folder_path}_{project_id}-home.md

CREATE subfolders (CRITICAL - create separately to avoid shell expansion issues):
  mkdir -p {folder_path}resources
  mkdir -p {folder_path}tasks
  mkdir -p {folder_path}zzz
```

### Confirmation Preview

```
**Proposed Project:**
- Project ID: {project_id}
- Title: {title}
- Folder: {VAULT_PATH}/Tracks/{project_id}/
- Subfolders: resources/, tasks/, zzz/

Review the content below before confirming:

[Full YAML shown below for reference]
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
3. Prompt user for task description
4. Extract from input: title, types, mentioned fields
5. **Auto-generate subtasks** from task description (see below)
6. Generate filename: `{YYYYMMDD}-{slug}.md`
7. Show proposed filename
8. Show draft with inferred values
9. Interactive edit loop until `done`
10. Output per file_operations setting

### Initial Prompt

```
Creating new task...

Describe the task in 1-2 sentences. I'll extract the title and generate fields.

Example: "Create SSL certificate renewal script that runs monthly and emails results"
Example: "Review Q4 budget and prepare presentation for board meeting"

What task would you like to create?
```

### Intelligent Field Extraction

The system intelligently extracts and infers:
- **Title:** Main task description
- **Types:** Based on keywords (vendor, infrastructure, security, etc.)
- **Phase:** planning/executing/testing/closing from context
- **Effort:** simple/moderate/complex from scope
- **Subtasks:** Automatically broken down from task description

**Subtask Auto-Generation:**
The system analyzes your task description and automatically creates logical subtasks. For example:

Input: "Schedule HVAC vendor site assessment and get quotes"
Auto-generated subtasks:
- [ ] Research qualified HVAC vendors
- [ ] Contact vendors for availability
- [ ] Schedule site assessment
- [ ] Receive and compare quotes

You can modify these during the interactive edit loop.

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
due [YYYY-MM-DD]     - Set due date
due clear            - Remove due date
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
- YYYY-MM-DD: Created task - {reason for creation}

---

### Subtasks
- [ ] {subtask}

---

### Notes

---
```

**Note on Task History:** Include rationale (why this task is needed) per RC principle.

Examples:
- `2025-12-21: Created task - SSL certs expiring next month`
- `2025-12-21: Created task - board meeting requires budget review`

**Output:** Per `cmd-output-behavior.md`

---

## !create object

### Execution Flow

1. Identify object type
2. **Determine placement FIRST** (ask before gathering fields)
3. Apply type-specific template
4. Populate fields from conversation/prompts
5. Use `TBD` for missing required fields
6. Interactive edit loop until `done`
7. Output per file_operations setting

### Syntax

`!create object [type]` or `!create [object-type]`

Examples:
- `!create object contact`
- `!create credit-card`
- `!create device`

### Placement Question (Asked First)

```
Creating new object ({type})...

Is this {type} cross-cutting (multiple tracks) or specific to one track?

Enter 'cross' for Objects/, or track name like 'p003-facl-hvac'
```

**Destination:**
- Cross-cutting: `{VAULT_PATH}/Objects/`
- Track-specific: `{VAULT_PATH}/Tracks/{track}/resources/`

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

### Field Requirements by Object Type

**contact:**
- Required: first_name, last_name
- Recommended: role, organization, email (for professional contacts), phone
- Optional: aliases, tags, notes

**device:**
- Required: device_type, identifier
- Recommended: manufacturer, model
- Optional: serial_number, purchase_date, warranty_expiration

**credit-card:**
- Required: issuer, name
- Recommended: last_four, credit_limit
- Optional: annual_fee, rewards_program

**account:**
- Required: institution, account_type
- Recommended: account_number (last 4), status
- Optional: balance, interest_rate

**medication:**
- Required: name
- Recommended: dosage, frequency, prescriber
- Optional: purpose, side_effects, start_date

**provider:**
- Required: name, provider_type
- Recommended: specialty, phone, address
- Optional: insurance_accepted, office_hours

### Example Prompts (contact)

```
Contact Information:

1. First name?
2. Last name?
3. Role/Title? (recommended for professional contacts)
4. Organization? (recommended for professional contacts)
5. Email? (optional)
6. Phone? (optional)
```

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

**Note:** full_name for contacts is auto-generated from first_name + last_name, not prompted separately.

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
