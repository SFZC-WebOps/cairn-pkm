# Creating Things
*Type: Write | Updated: 2025-01-01*

## Overview

Create areas, projects, tasks, and objects through natural conversation. The system extracts what it can from context and asks only for what's missing.

**Shortcuts:** `!create area`, `!create project`, `!create task`, `!create object [type]`

---

## Conversational Creation

### How It Works

User describes what they need → LLM extracts details → Shows draft → Confirms before saving.

```
User: I need to track my Q1 budget work

LLM: I'll create a project for the Q1 budget.

     Here's what I'm thinking:
     
     Project: p017-q1-budg
     Title: Q1 Budget
     Description: Q1 budget work
     
     A few questions:
     - When do you need this done? (rough end date)
     - How much of your time will this take? (percentage)
     
     (Or say "looks good" to create with defaults)

User: End of January, probably 20% of my time

LLM: Got it. Here's the project:
     
     📁 Tracks/p017-q1-budg/
     
     ---
     project_id: p017-q1-budg
     title: Q1 Budget
     status: active
     progress: 0
     end_date: 2025-01-31
     effort_percent: 20
     ---
     
     Create this? (yes / adjust something)
```

### What Gets Extracted

From user input, infer:

| Input Pattern | Extracted |
|---------------|-----------|
| "Q1 budget" / "website redesign" | Title, project codes |
| "by end of January" / "due Friday" | Dates |
| "high priority" / "urgent" | Priority |
| "takes about 20%" / "half my time" | effort_percent |
| "for Martinez" / "CEO wants this" | stakeholder |
| "I'm working on..." / "for the X project" | Track context |

### What Gets Asked

Only ask for what's:
- Required and can't be inferred (e.g., which project for a task)
- Important and wasn't mentioned (e.g., due date for something urgent)

Don't ask for everything. Use sensible defaults.

---

## Creating Areas

An area is an ongoing domain (doesn't end).

**From conversation:**
```
User: I need to track my personal finances

LLM: I'll create an area for personal finance.
     
     📁 Tracks/area-finance/
        📄 _area-finance-home.md
        📁 tasks/
        📁 resources/
        📁 zzz/
     
     Create this?
```

**Shortcut:** `!create area`

### Validation

- Domain name: 3-15 lowercase letters (a-z only)
- No spaces, numbers, or special characters
- Examples: finance, health, webops, personal

### Template

```yaml
---
title: {generated}
type: area
status: active
created: {date}
modified: {date}
---

## Overview
{description}

## Current Focus

## Notes

---

## Log

---
{date} - Setup - Area created
---
```

---

## Creating Projects

A project is temporary work with a goal and end date.

**From conversation:**
```
User: We're migrating the blog to a new platform, should take about 3 months

LLM: I'll create a project for the blog migration.
     
     Next project number is 018.
     
     📁 Tracks/p018-blog-migr/
        📄 _p018-blog-migr-home.md
        📁 tasks/
        📁 resources/
        📁 zzz/
     
     Project details:
     - Title: Blog Migration
     - Timeline: ~3 months (end date: April 2025)
     - Status: planning
     
     Who's the main stakeholder? (or skip if n/a)
```

**Shortcut:** `!create project`

### Project Naming

Format: `p###-{system}-{action}`

- `###` = Project number (user chooses)
- `{system}` = What's being changed (3-5 letters: blog, site, infra)
- `{action}` = What's being done (3-5 letters: migr, upgr, rdsg)

**Ask the user for the number:**
```
LLM: What project number do you want? 
     (You have p001 through p003 already, but you can pick any number)
```

Users may have non-linear numbering schemes or reserved ranges.

**Examples:**
- p018-blog-migr (blog migration)
- p019-site-rdsg (site redesign)
- p020-infr-upgr (infrastructure upgrade)

### Code Validation

- 3-5 lowercase letters only
- No numbers, spaces, hyphens within codes

### Template

```yaml
---
project_id: {id}
title: {title}
type: project
status: planning
progress: 0
summary: ""
created: {date}
modified: {date}
start_date: {date}
end_date: {date}
effort_percent: {percent}
stakeholder: {name}
category: {category}
flexibility: negotiable
---

## Overview
{description}

## Current Focus

## Notes

---

## Log

---
{date} - Setup - Project created
---
```

### Capacity Fields

These are optional but help with capacity planning:

| Field | Purpose | Example |
|-------|---------|---------|
| effort_percent | How much of your time (0-100) | 25 |
| peak_months | When effort is highest | [4, 5] |
| stakeholder | Whose priority this serves | "Martinez" |
| category | Type of work | operations, development |
| flexibility | How moveable | fixed, negotiable, flexible |

---

## Creating Tasks

A task is a work item within a project or area.

**From conversation:**
```
User: I need to update the SSL certificate before it expires Friday

LLM: I'll create a task for the SSL certificate update.
     
     Which project is this for?

User: The website redesign

LLM: Got it. Here's the task:
     
     📄 Tracks/p014-site-rdsg/tasks/20250101-ssl-cert-update.md
     
     ---
     title: Update SSL certificate
     project: p014-site-rdsg
     priority: high
     due_date: 2025-01-03
     status: active
     ---
     
     ### Task History
     - 2025-01-01: Created — cert expires Friday
     
     ### Subtasks
     - [ ] Generate new certificate
     - [ ] Update server configuration
     - [ ] Verify renewal works
     
     Create this?
```

**Shortcut:** `!create task`

### What Gets Inferred

| From Input | Field |
|------------|-------|
| "urgent" / "ASAP" / "critical" | priority: critical |
| "by Friday" / "due Jan 15" | due_date |
| "for John" | assignee |
| Description content | Subtasks (auto-generated) |
| "blocked by X" | status: blocked |

### Subtask Suggestions

After creating the basic task, offer to suggest subtasks:

```
LLM: Want me to suggest some subtasks to get started?

User: Sure

LLM: Based on "Migrate database to new server", here are some steps:
     
     - [ ] Backup current database
     - [ ] Provision new server
     - [ ] Test migration process
     - [ ] Execute migration
     - [ ] Verify data integrity
     - [ ] Update connection strings
     
     Add these? (yes / modify / skip)
```

User can:
- Accept as-is
- Modify ("remove the backup one, I already did that")
- Add their own ("also add 'notify stakeholders'")
- Skip entirely

### Template

```yaml
---
title: {title}
project: {track}
created_date: {date}
due_date: {date}
assignee: {name}
parent_task: 
priority: {priority}
status: active
phase: {phase}
effort: {effort}
viz: 
section: 
type:
  - {types}
---

### Task History
- {date}: Created — {rationale}

---

### Subtasks
- [ ] {subtask}

---

### Notes

---
```

### Field Values

See `cmd-shared-patterns.md` Field Enums for valid values:
- status: active, blocked, complete, deferred, onhold, review, scheduled, waiting
- priority: low, medium, high, critical
- phase: planning, executing, testing, closing
- effort: simple, moderate, complex
- viz: now, next, soon, later, blocked, waiting

---

## Creating Objects

Objects are things that span multiple projects (contacts, accounts, devices).

**From conversation:**
```
User: I need to add a new vendor contact — Jennifer Martinez from Acme Corp

LLM: I'll create a contact for Jennifer Martinez.
     
     Is this specific to one project, or will you reference her across 
     multiple projects?

User: Multiple — she's our main Acme contact

LLM: I'll put her in Objects/ then.
     
     📄 Objects/contact-jennifer-martinez.md
     
     ---
     type: contact
     first_name: Jennifer
     last_name: Martinez
     organization: Acme Corp
     role: 
     email:
     phone:
     status: active
     ---
     
     Want to add role, email, or phone? (or "looks good" to create)
```

**Shortcut:** `!create object contact`, `!create object device`, etc.

### Placement Decision

- **Objects/** — Cross-cutting, used by multiple tracks
- **{track}/resources/** — Specific to one project

Always ask if unclear.

### Supported Types

| Type | Filename Pattern |
|------|------------------|
| contact | contact-{firstname-lastname}.md |
| account | account-{institution}-{type}.md |
| device | device-{type}-{identifier}.md |
| vendor | vendor-{company}.md |
| subscription | subscription-{service}.md |
| credit-card | credit-card-{issuer}-{name}.md |
| medication | medication-{name}.md |
| provider | provider-{name}.md |

### Object Templates

Each type has specific fields. See `_cairn-pkm/templates/` for full templates.

**Base structure:**
```yaml
---
type: {object-type}
status: active
created: {date}
lastmod: {date}
aliases: []
tags: []
---

## Overview

## Details

## Notes
```

---

## Confirmation Before Save

**Always show what will be created and get explicit confirmation.**

```
LLM: Here's the [entity]:
     
     [Show filename and path]
     [Show full content]
     
     Create this? (yes / adjust something)
```

Accept: "yes", "looks good", "create it", "save"
Adjust: "change the priority to critical", "add a subtask for X"

---

## Output Behavior

After confirmation:

1. Check prefs for file_operations setting
2. If "write" and filesystem available → Write directly, confirm success
3. If "download" or no filesystem → Show content, provide download link
4. Always show where file should be placed

```
LLM: ✓ Created Tracks/p018-blog-migr/

     The project is ready. Want to add tasks, or do something else?
```

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| Can't determine type | "I'm not sure what to create — is this a project, task, area, or something else?" |
| Missing project context for task | "Which project should this task go in?" |
| Duplicate would be created | "There's already an area-finance. Want to open it instead?" |
| Invalid name | "That name won't work because [reason]. How about [suggestion]?" |
