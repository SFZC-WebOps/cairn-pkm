# Shared Command Patterns
*Type: Include | Updated: 2025-01-01*

Reference document for patterns used across multiple commands. Commands should reference this rather than duplicating.

---

## The Mantra

1. **Vault holds truth** — All data lives in plain markdown files with YAML frontmatter
2. **LLM reasons over it** — Loads, analyzes, simulates, generates outputs
3. **Conversation is the interface** — Natural language, not menus or syntax
4. **Always confirm before changing** — Show what will happen, get explicit approval

---

## Interaction Model

### Conversation First

Users interact through natural language. The system reveals itself through dialogue, not documentation.

**Instead of:**
```
!create task
> Title?
> Priority? (low/medium/high/critical)
> Due date? (YYYY-MM-DD)
```

**Do this:**
```
User: I need to update the SSL cert before it expires Friday

LLM: I'll create a task for that. Here's what I'm thinking:

     Title: Update SSL certificate
     Project: (which project is this for?)
     Priority: high (expires Friday)
     Due: 2025-01-03
     
     What project should this go in?
```

### Command Shortcuts

Commands (`!hi`, `!create`, `!bye`, etc.) exist as shortcuts for experienced users who know what they want and don't need guidance. They skip the conversational discovery and go direct.

**Behavior:**
- If user issues a command → Execute it directly, assume they know what they're doing
- If user speaks naturally → Engage conversationally, teach as needed

**The commands are conveniences, not the primary interface.**

### Context Establishment

At cold start or when context is unclear:

```
LLM: Are you picking up an existing project or starting something new?

User: Existing - the website redesign

LLM: [Looks up matching projects]
     
     Found p014-site-rdsg. Last activity was Dec 28 — 
     "Sprint 3 complete, starting QA." 4 active tasks.
     
     What do you need to do?
```

**Rules:**
- Don't assume context — ask
- Look up what the user references — confirm you found the right thing
- Surface relevant state — what's the current situation?

### Confirmation Before Changes

**Every write operation requires explicit confirmation.**

```
User: Mark the SSL task complete

LLM: I'll update 20251220-ssl-cert-fix.md:
     
     Changes:
     - status: active → complete
     - Add history: "2025-01-01: Marked complete"
     
     Save this? (or tell me what to adjust)
```

**Rules:**
- Show the specific file being changed
- Show exactly what will change (before → after)
- Wait for explicit "yes" / "save it" / "do it" before writing
- If user says "no" or asks for changes → adjust and re-confirm

### Conversational Error Recovery

When something goes wrong, stay in conversation:

**Instead of:**
```
ERROR: File not found: 20251220-ssl-fix.md
```

**Do this:**
```
LLM: I couldn't find a task called "ssl-fix" — there's one called 
     "ssl-cert-renewal" from December 20th. Is that the one?
```

**Rules:**
- Explain what happened in plain language
- Offer likely alternatives if available
- Ask clarifying questions to recover
- Never dead-end with just an error code

### Session Awareness

Track what's happening in the conversation:

**During a session, maintain:**
- Current project/area context
- Files viewed, created, or modified
- Decisions made
- Open questions or action items

**When wrapping up seems natural:**
```
LLM: Looks like we've covered what you needed — you created 2 tasks 
     and updated the project status. Want me to wrap up and 
     generate a session log?
```

**Or user can shortcut:** `!bye`

### Teaching Through Use

New users don't need a tutorial. They learn by doing:

```
User: how do I track a new project?

LLM: I can set that up for you. What's the project about?

User: We're migrating the blog to a new platform

LLM: Got it. I'll create a project for the blog migration.
     
     A few quick questions:
     - What timeframe are you thinking? (rough start/end)
     - How much of your capacity will this take? (percentage)
     - Who's the main stakeholder?
     
     (These help with planning later, but we can skip any that 
     don't apply)
```

The system explains *why* it's asking, not just *what* it needs.

---

## Robustness Principles

Three principles inform all command implementations. These derive from systems engineering practices for reliable operations.

### Verify-Before-Modify (VBM)

Before any write operation, confirm state matches expectations:

```
BEFORE file write:
  VERIFY: target_path status (exists | not_exists) matches intent
  VERIFY: current_track context is correct
  STATE: "{operation} {filename} in {path}"
  THEN: execute operation
```

**Purpose:** Catches wrong-target writes and context confusion before they happen.

**Example output:**
```
Creating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/
```

**VBM Examples for All Write Operations:**

```
BEFORE task creation:
  VERIFY: Track context is correct (p014-blog-migr)
  VERIFY: tasks/ directory exists
  STATE: "Creating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/"
  EXECUTE: Write operation
  VERIFY: File created successfully

BEFORE task edit:
  VERIFY: Target file exists (20251220-ssl-fix.md found in tasks/)
  VERIFY: File is parseable (YAML frontmatter valid)
  STATE: "Updating 20251220-ssl-fix.md in Tracks/p014-blog-migr/tasks/"
  EXECUTE: Write operation
  VERIFY: File updated successfully

BEFORE area creation:
  VERIFY: area-{domain} does not already exist
  STATE: "Creating Tracks/area-facilities/"
  EXECUTE: mkdir, file creation
  VERIFY: Directory and home doc created

BEFORE object creation:
  VERIFY: Placement decision confirmed (Objects/ or track/resources/)
  VERIFY: Target directory exists
  STATE: "Creating contact-jennifer-martinez.md in Tracks/p003-facl-hvac/resources/"
  EXECUTE: Write operation
  VERIFY: File created successfully
```

### Graceful Fallback Chain (GFC)

When operations fail, degrade gracefully with user visibility:

```
ON FAILURE:
  1. Attempt next fallback mode in chain
  2. Report current position in fallback chain
  3. Preserve and display content
  4. NEVER lose content silently
```

**Fallback chain:** write → download → display

**Purpose:** User never loses work due to system/permission errors.

See `cmd-output-behavior.md` for implementation details.

### Rationale Capture (RC)

Log entries should capture *why*, not just *what*:

```
GOOD: "2025-12-20: Deferred migration - waiting on vendor API access"
BAD:  "2025-12-20: Deferred migration"

GOOD: rationale: "Certificates expiring caused service outage"
BAD:  (no rationale field)
```

**Purpose:** Future-you (and collaborators) understand decision context.

---

## Field Enums

Canonical values for enumerated fields. Commands reference this section rather than maintaining separate copies.

### Task Fields

| Field | Values |
|-------|--------|
| status | active, blocked, complete, deferred, onhold, review, scheduled, waiting |
| priority | low, medium, high, critical |
| phase | planning, executing, testing, closing |
| effort | simple, moderate, complex |
| viz | now, next, soon, later, blocked, waiting |

### Area Fields

| Field | Values |
|-------|--------|
| status | active, onhold, archived |
| review_cycle | weekly, monthly, quarterly, yearly |

### Project Fields

| Field | Values |
|-------|--------|
| status | planning, active, onhold, complete, archived |
| category | operations, development, planning, event, compliance |
| flexibility | fixed, negotiable, flexible |

### Object Fields

| Field | Values |
|-------|--------|
| status | active, inactive, archived |

### Log Entry Types

| Type | Use For |
|------|---------|
| decision | Choices made with rationale |
| update | General progress or changes |
| milestone | Significant completions |
| issue | Problems encountered/resolved |
| note | Reference information |

---

## Rationale Capture (RC) Principle - Usage Guide

RC principle applies to these entry types:

### Task History

**Format:** `YYYY-MM-DD: {action} - {reason}`

**Required:** Always include reason

**Examples:**
- "2025-12-21: Increased priority - blocking launch"
- "2025-12-21: Set viz to blocked - waiting on vendor"
- "2025-12-21: Updated due date - aligned with project deadline"
- "2025-12-21: Marked complete - verified in production"

### Changelog

**Field:** `rationale: "{why change was necessary}"`

**Required:** Always populate

**Examples:**
- `rationale: "Certificates expiring caused service outage"`
- `rationale: "Performance degradation impacting user experience"`
- `rationale: "Security audit requirement"`
- `rationale: "Project dependency for Q2 launch"`

### Log Entries (Project/Area)

**Format:** `Type - {what} - {why/context}`

**Recommended:** Include context when non-obvious

**Examples:**
- "Update - Email template assigned - needed for Jan 15 launch"
- "Decision - Switching to quarterly reviews - monthly too frequent"
- "Milestone - Migration complete - ready for testing"
- "Issue - SSL certificate expired - renewed and monitoring added"

### When RC is Optional

- Simple status updates ("Setup", "Closed", "Created")
- Routine maintenance logs
- Self-explanatory actions
- Obvious outcomes

**Examples where RC not needed:**
- "Setup - Area created"
- "Closed - Session ended"
- "Created - Task added"

---

## Initialization Pattern

All commands that use user preferences:

```
READ: /mnt/project/cairn-pkm-user-prefs.yaml
EXTRACT:
  - file_operations (default: "download")
  - vault_location (default: "local")
  - cloud_vault_path (default: "")
  - timezone (default: "America/Los_Angeles")
  - default_assignee (default: "")

ON FILE NOT FOUND: Use defaults, continue execution
```

**Preferences File Location:**

Commands read from `/mnt/project/cairn-pkm-user-prefs.yaml` (the uploaded project file).

**Template location:** `_cairn-pkm/llm/uploads/cairn-pkm-user-prefs.yaml`

**User workflow:**
1. Edit template in `_cairn-pkm/llm/uploads/`
2. Upload to LLM project
3. Commands read settings from project files

---

## DateTime Output

When command needs to display current time:

```
OUTPUT: "📋═ Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
```

---

## Completion Pattern

After completing a discrete action:

```
LLM: ✓ Done — [brief description of what was accomplished]
     
     [If relevant: suggest natural next step or ask what's next]
```

**Examples:**

```
✓ Done — created the SSL renewal task in p014-blog-migr.

✓ Done — updated project status to "on hold" and added the log entry.
   Want me to notify anyone or capture why it's paused?

✓ Done — here's the capacity report for Q2. 
   [Download link]
```

**No robotic "waiting for next instruction" — just natural conversation flow.**

---

## File Output

All file creation/editing uses the OUTPUT_FILE pattern from `cmd-output-behavior.md`.

```
APPLY: VBM principle (verify target, state operation)
CONSTRUCT: filepath = {target path}
CONSTRUCT: content = {file content}
CALL: OUTPUT_FILE(filepath, content)
ON FAILURE: GFC principle (fallback chain with visibility)
```

Output varies by `file_operations` setting (display/download/write/confirm). See `cmd-output-behavior.md` for behavior details and fallback handling.

---

## Error Recovery

Errors are part of conversation, not dead ends.

### Conversational Recovery Patterns

| Situation | Response |
|-----------|----------|
| File not found | "I couldn't find [x] — did you mean [y]? Or tell me more about what you're looking for." |
| Ambiguous reference | "There are a few things that could match. Which one: [list]?" |
| Parse error | "That file has a formatting issue — [explain]. Want me to try to fix it?" |
| Write failed | "I couldn't save that — [reason]. Here's the content so you don't lose it: [show content]" |
| Missing context | "Which project is this for?" / "Is this new or existing?" |

### GFC Still Applies

When write operations fail, fall back gracefully:

```
LLM: I couldn't write directly to your vault — looks like I don't have 
     filesystem access right now. 
     
     Here's the file ready to download:
     [Download link]
     
     Save it to: Tracks/p014-blog-migr/tasks/
```

**Never lose content. Always show what was created even if saving fails.**

### No Dead Ends

Every error should offer a path forward:

- Suggest alternatives
- Ask clarifying questions  
- Show what you have so far
- Offer to try a different approach

---

## Encoding Requirement

Before presenting any markdown file to user:

```python
import ftfy
content = ftfy.fix_text(content)
```

This prevents mojibake (double-encoded UTF-8). Non-negotiable.

### Encoding Verification

All commands that create or edit files must apply ftfy encoding fix:

**Standard pattern:**
```python
import ftfy

# Before writing/presenting file
content = ftfy.fix_text(content)

# Then write or display
```

**Purpose:** Prevents mojibake characters (garbled text like Ã, â€, etc.) from appearing in markdown files.

**When to apply:**
- After generating any file content
- Before writing to filesystem
- Before displaying to user
- After reading files for editing

---

## Vault Path Reference

| Alias | Meaning |
|-------|---------|
| `{VAULT_PATH}` | Root of user's Obsidian vault |
| `{track}` | Current area or project folder name |

Common paths:
- Tasks: `{VAULT_PATH}/Tracks/{track}/tasks/`
- Resources: `{VAULT_PATH}/Tracks/{track}/resources/`
- Capture: `{VAULT_PATH}/Capture/`
- Objects: `{VAULT_PATH}/Objects/`
- Changelog: `{VAULT_PATH}/_local/data/changelog/`
