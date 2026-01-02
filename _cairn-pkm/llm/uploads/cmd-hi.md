# Session Start
*Type: Interactive | Updated: 2025-01-01*

## Overview

Entry point for working with Cairn-PKM. The system meets users where they are — new users get guidance, experienced users can go direct.

**Shortcut:** `!hi` or `!hi-[track]` for direct access

---

## Conversational Entry

When a user starts a conversation (or says something like "let's work" or "hi"):

```
LLM: Are you working on something, or would you like a tour of Cairn-PKM?
```

**If user wants tour:** Run through tour (see cmd-tour.md), then:
```
LLM: That's the basics. Want to start tracking a project, 
     or is there something I should explain more?
```

**If user names something:** Proceed to context discovery (below).

### Context Discovery

Based on user response, establish context:

**User names a project/area:**
```
User: The website redesign

LLM: [Searches for matching tracks]

     Found p014-site-rdsg — "Website Redesign Project"
     Last activity Dec 28: "Sprint 3 complete, starting QA"
     4 active tasks, 1 overdue.
     
     What do you need to do?
```

**User describes something new:**
```
User: I need to start tracking a new initiative

LLM: Tell me about it — what's the initiative?

User: We're migrating our documentation to a new platform

LLM: Got it. I can set up a project for the docs migration.
     
     A few quick questions to get started:
     - Rough timeframe? (when do you want this done?)
     - How much of your time will this take? (ballpark percentage)
     - Who's the main stakeholder?
     
     (Skip any that don't apply yet)
```

**User wants to see what's active:**
```
User: What do I have going on?

LLM: [Scans Tracks/ for active projects and areas]

     You have 3 active projects:
     - p014-site-rdsg: Website redesign (75% complete, QA phase)
     - p015-docs-migr: Docs migration (planning, not started)
     - p016-q1-budg: Q1 budget (active, due Jan 15)
     
     And 2 ongoing areas:
     - area-finance: 2 active tasks
     - area-webops: 5 active tasks
     
     Which one do you want to look at?
```

**User asks a question:**
```
User: How do I track my time on projects?

LLM: Cairn uses an effort_percent field in project frontmatter — 
     it's the percentage of your capacity a project takes.
     
     For example, if something takes about a day a week, that's 20%.
     The !capacity command shows how your time adds up across projects.
     
     Want me to show you your current capacity breakdown?
```

---

## Shortcut: !hi

For experienced users who want to skip the conversation:

```
!hi
```

**Behavior:** 
- If context is already established → Show current track overview
- If no context → "What are you working on today?"

---

## Shortcut: !hi-[track]

Direct access to a specific track:

```
!hi-p014-site-rdsg
!hi-area-finance
```

**Behavior:** Load and display the track overview immediately.

---

## Track Overview Display

When showing a track (via conversation or `!hi-[track]`):

### What to Show

```
READ: {VAULT_PATH}/Tracks/[target]/_*-home.md
READ: All task files in [target]/tasks/
PARSE: Task hierarchy (parent-child relationships)
FILTER: status != complete (unless specifically asked for archive)
SORT: priority (critical → low), then created_date
```

### Format

```markdown
## [Track Name]

**Status:** [status] | **Progress:** [progress]% | **Type:** [area|project]
**Summary:** [summary if set]

### What's Happening

[Current Focus section from home doc, or Overview if no current focus]

### Recent Activity

[Last 5-10 log entries, most recent first]

### Active Tasks ([N] total, [X] overdue)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 [Task Title]
   Status: [status] | Priority: [priority] | Due: [date or "not set"]
   [If overdue: ⚠️ X days overdue]
   
   Subtasks: [N pending, M complete]
   └─ [ ] Next subtask if relevant
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[Repeat for each active task]

### Quick Stats

- By status: [N] active, [N] blocked, [N] waiting
- Overdue: [N] tasks
- Due this week: [N] tasks
```

### After Overview

Don't show a numbered menu. Just end with context acknowledgment:

```
LLM: [Shows track overview]

     Context set to p014-site-rdsg. What do you need to do?
```

User can then:
- Ask to create/edit things naturally
- Use command shortcuts (`!create task`)
- Ask questions
- Switch tracks

---

## Date Handling

**Timezone:** Read from cairn-pkm-user-prefs.yaml, default to America/Los_Angeles

**Overdue:** due_date < current_date

**Due this week:** due_date <= current_date + 7 days

**Display:**
- Normal overdue: "⚠️ 5 days overdue"
- Critically overdue (>90 days): "⚠️ Critically overdue — needs review"

---

## Session Tracking

Throughout the conversation, maintain awareness of:

```
- Current track context
- Files viewed, created, modified
- Decisions made
- Open questions or next steps
```

This context informs:
- Natural suggestions ("want me to mark that complete?")
- Session wrap-up (`!bye` or "let's wrap up")
- What to log

---

## Task Display Details

### Required Frontmatter

| Field | Type | Required |
|-------|------|----------|
| title | string | Yes |
| project | string | Yes |
| created_date | date | Yes |
| status | enum | Yes |
| priority | enum | Yes |

### Optional Frontmatter

due_date, assignee, phase, effort, viz, parent_task

See `cmd-shared-patterns.md` Field Enums for valid values.

### Parent-Child Display

```
📋 Parent Task Title
   Status: active | Priority: high | Due: Jan 15
   
   Child tasks:
   └─ Child task 1 (active, medium)
   └─ Child task 2 (complete)
```

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| Track not found | "I couldn't find '[name]' — did you mean one of these? [list similar]" |
| No tasks | "No active tasks in [track]. Want to create one?" |
| Parse error | "Had trouble reading [file] — there might be a formatting issue. Want me to take a look?" |
| Ambiguous | "There are a few things called [x]. Which one: [list]?" |

---

## Integration

| User Intent | Behavior |
|-------------|----------|
| "Create a task" | Use current track context, guide through creation |
| "Mark X complete" | Find the task, confirm the change |
| "Add a note" | Determine if task note or track log entry |
| "I'm done" | Offer to wrap up session |
| "Show me capacity" | Run capacity analysis |
| "What's overdue?" | Filter and show overdue tasks |
