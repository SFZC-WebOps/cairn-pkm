# !tour - Guided Onboarding
*Type: Interactive | Updated: 2025-12-22*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!tour` | Start guided tour of Cairn-PKM |
| `!tour [step]` | Jump to specific step (1-5) |

**Duration:** 5-7 minutes
**Mode:** Non-destructive (display only, regardless of user prefs)

---

## Overview

Interactive walkthrough for new users. Teaches core concepts through hands-on exploration of sample data. Creates nothing permanent"”shows what *would* happen.

**Philosophy:** Learn by doing, not reading. Cover the 80/20.

---

## Initialization

Per `cmd-shared-patterns.md`, plus:

```
OVERRIDE: file_operations = "display" (tour mode)
SET: tour_active = true
SET: current_step = 1
```

---

## Tour Controls

Numerical menu at each step:

| Input | Action |
|-------|--------|
| `1` | Continue to next step |
| `2` | Skip current step |
| `3` | Go back to previous step |
| `4` | Exit tour |

Step 3 (task creation) has special input handling for task description.

---

## Execution

### Welcome

```
OUTPUT:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸ—ºï¸  CAIRN-PKM TOUR
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

Welcome! This 5-minute tour will show you how Cairn-PKM works.

You'll learn to:
"¢ Navigate the vault structure
"¢ View a project and its tasks
"¢ Create a task (preview only"”nothing saved)
"¢ Close a session properly

Everything is non-destructive. You're just exploring.

1. Start tour
2. Exit

Enter number:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
```

### Step 1: Orient (30-60 sec)

```
OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
STEP 1 of 5: THE VAULT STRUCTURE
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

Cairn-PKM organizes everything into five domains:

ðŸ“ _cairn-pkm/     System files (don't edit"”replace to update)
ðŸ“ _local/         Your customizations (templates, data, views)
ðŸ“ Capture/        Inbox for quick notes
ðŸ“ Objects/        Cross-cutting entities (contacts, devices, etc.)
ðŸ“ Tracks/         Where work lives

Inside Tracks/, you have two types:

  ðŸ“‚ area-{name}/      Ongoing domains (finance, health, work)
  ðŸ“‚ p###-{sys}-{act}/ Temporary projects (p001-blog-migr)

Each track has the same internal structure:
  _*-home.md    â† Source of truth
  resources/    â† Supporting materials  
  tasks/        â† Work items
  zzz/          â† Completed/archived

That's it. Five domains, two track types, consistent structure.

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
1. Continue
2. Skip this step
3. Exit tour

Enter number:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
```

### Step 2: Explore a Track (1-2 min)

```
OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
STEP 2 of 5: EXPLORING A PROJECT
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

Let's look at a sample project. In real use, you'd type:

  !hi-p002-prod-lnch

I'll show you what that displays...

EXECUTE: 
  READ: {VAULT_PATH}/Tracks/p002-prod-lnch/_p002-prod-lnch-home.md
  READ: All tasks in p002-prod-lnch/tasks/
  DISPLAY: Track overview (same format as !hi-[target])

OUTPUT after track display:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

What you just saw:

"¢ **Overview** "” What this project is about
"¢ **Current Focus** "” What's happening now
"¢ **Recent Activity** "” Log entries with timestamps
"¢ **Active Tasks** "” All open work items with status, priority, due dates
"¢ **Task Summary** "” Counts by status and priority

The !hi-[track] command is your main way to see what's happening.

TIP: Notice how tasks show parent-child relationships and subtasks.
     This helps you see both the big picture and the details.

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
1. Continue
2. Skip this step  
3. Go back
4. Exit tour

Enter number:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
```

### Step 3: Create a Task (2-3 min)

```
OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
STEP 3 of 5: CREATING A TASK
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

Now let's create a task. In real use, you'd type:

  !create task

Then describe what you need. Let me walk you through it...

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

Creating new task...

Describe the task in 1-2 sentences, or:

1. Use example task
2. Skip this step
3. Go back
4. Exit tour

Enter description or number:

PARSE INPUT:
  IF "1" or "example": USE "Schedule stakeholder demo to review portal features before launch"
  IF "2": SKIP to Step 4
  IF "3": GO BACK to Step 2
  IF "4": EXIT tour
  ELSE: USE input as task description

OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

I extracted from your description:

**Proposed Task:**
- Title: Schedule stakeholder demo for portal review
- Track: p002-prod-lnch (from tour context)
- Priority: medium (default)
- Status: active (default)
- Phase: planning (inferred from "schedule")

**Auto-generated subtasks:**
- [ ] Identify key stakeholders to invite
- [ ] Check calendars for available slots
- [ ] Send meeting invite with agenda
- [ ] Prepare demo environment
- [ ] Document feedback from demo

GENERATE and display full task file:

ðŸ“„ FILE PREVIEW (tour mode"”not saved)
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
Filename: 20251222-schedule-stakeholder-demo.md
Path: Tracks/p002-prod-lnch/tasks/20251222-schedule-stakeholder-demo.md

---
title: "Schedule stakeholder demo for portal review"
project: "p002-prod-lnch"
created_date: 2025-12-22
due_date: 
assignee: ""
parent_task: 
priority: medium
status: active
phase: planning
effort: simple
viz: 
type:
  - coordination/scheduling
---

### Task History
- 2025-12-22: Created task - stakeholder approval needed before launch

---

### Subtasks
- [ ] Identify key stakeholders to invite
- [ ] Check calendars for available slots
- [ ] Send meeting invite with agenda
- [ ] Prepare demo environment
- [ ] Document feedback from demo

---

### Notes

---
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

In real use, you could edit any field before saving:
  "¢ priority high
  "¢ due 2025-01-15
  "¢ subtask Add another item
  "¢ done (to save)

This is PREVIEW ONLY. Nothing was saved.

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
1. Continue
2. Skip this step
3. Go back
4. Exit tour

Enter number:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
```

### Step 4: Close a Session (1 min)

```
OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
STEP 4 of 5: CLOSING A SESSION
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

When you're done working, close with:

  !bye

This does three things:

1. **Summarizes** what happened (tracks visited, files touched)
2. **Generates a task history entry** you can paste into tasks
3. **Creates a log entry** for the track's home document

Here's what !bye would produce for this tour session:

GENERATE sample !bye output:

ðŸ“Š SESSION SUMMARY
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

Tracks visited: p002-prod-lnch
Files created: 0 (tour mode)
Files edited: 0
Log entries added: 0
Key decisions: Explored task creation workflow

â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

ðŸ“ SUGGESTED TASK HISTORY ENTRY:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
2025-12-22: Completed Cairn-PKM tour - learning system basics

Copy to relevant task or track catch-all task.
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

Why close sessions? It builds a log of what you did and why.
Your future self will thank you.

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
1. Continue
2. Skip this step
3. Go back
4. Exit tour

Enter number:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
```

### Step 5: Next Steps (30 sec)

```
OUTPUT:
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
STEP 5 of 5: WHAT'S NEXT
â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€

You've seen the core workflow:

  !hi          â†’ Open menu or view a track
  !create      â†’ Make areas, projects, tasks, objects
  !edit        â†’ Modify anything
  !bye         â†’ Close session with logging

Other commands to explore:

  !capture     â†’ Save ideas and links quickly
  !changelog   â†’ Document system changes
  !skills      â†’ Track skill evidence
  !help        â†’ Command reference

**Recommended first steps:**

1. Create your first area:
   !create area
   
   Start with something like "personal" or "work"

2. Add a real task:
   !create task
   
   Something you actually need to do

3. Explore the sample project more:
   !hi-p002-prod-lnch
   
   Try editing a task: !edit [filename]

**Getting help:**

"¢ _ARCHITECTURE.md "” Full system documentation
"¢ _INSTALLATION.md "” Setup and configuration
"¢ cmd-*.md files "” Detailed command specs

â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€â”€
```

### Completion

```
OUTPUT:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•
ðŸŽ‰ TOUR COMPLETE
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

You're ready to use Cairn-PKM.

Remember:
"¢ Start small "” one area, a few tasks
"¢ Grow organically "” add structure when you need it
"¢ Close sessions with !bye "” your logs will thank you

What's next?

1. Start working (!hi)
2. Create your first area (!create area)
3. Retake tour (!tour)

Enter number or any command:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

CLEAR: tour_active = false

IF input is 1: EXECUTE !hi
IF input is 2: EXECUTE !create area
IF input is 3: EXECUTE !tour
ELSE: EXECUTE input as command

STOP
```

---

## Early Exit Handling

```
ON input "4" (exit) at any step:

OUTPUT:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

Tour ended early. No problem!

You completed: Steps 1-{current_step - 1}
You skipped: Steps {current_step}-5

What's next?

1. Resume tour (!tour)
2. Start fresh (!tour 1)  
3. Start working (!hi)
4. Create first area (!create area)

Enter number or any command:
â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•

CLEAR: tour_active = false

IF input is 1: EXECUTE !tour (resumes at current_step)
IF input is 2: EXECUTE !tour 1
IF input is 3: EXECUTE !hi
IF input is 4: EXECUTE !create area
ELSE: EXECUTE input as command

STOP
```

---

## Step Jump Handling

```
ON "!tour [N]" where N is 1-5:

SET: current_step = N
EXECUTE: Step N

ON "!tour [N]" where N is invalid:

OUTPUT: "Invalid step. Use !tour 1 through !tour 5"
```

---

## Sample Data Dependency

The tour uses the example content included in the distribution:

- `Tracks/area-admin/` "” Example area
- `Tracks/p002-prod-lnch/` "” Example project (primary tour focus)
- `Objects/` "” Sample object files
- `Capture/` "” Example quick notes

If user has already deleted sample data, the tour gracefully degrades (see Error Handling).

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Sample data missing | "Sample project (p002-prod-lnch) not found. Already cleaned up? No problem"”I'll demonstrate with a hypothetical example instead." Then continue with simulated data. |
| Sample data partial | Use what exists, simulate what's missing |
| Step out of range | "Invalid step. Use !tour 1 through !tour 5" |
| Unknown command during tour | "Tour command not recognized. Type 'help' for controls." |
| User tries to save during tour | "Tour mode is preview-only. Exit tour (!done) to create real files." |

**Graceful degradation:** If sample data has been deleted, the tour should still work by showing simulated/example output rather than failing. The learning value is in seeing the *format* and *workflow*, not the specific sample content.

---

## Integration

| Works With | How |
|------------|-----|
| !hi | Tour demonstrates !hi-[target] usage |
| !create | Tour simulates !create task flow |
| !bye | Tour shows !bye output format |
| Sample data | Requires p002-prod-lnch with tasks |

---

## Design Notes

**Why linear?** New users don't know what they don't know. A menu would require decisions they can't make yet.

**Why display-only?** Removes fear of breaking things. They can experiment freely, then commit when ready.

**Why 5 steps?** Cognitive limit. More steps = more dropoff. These five cover the essential loop: navigate â†’ create â†’ close.

**Why sample project?** Abstract explanations don't stick. Seeing real (sample) data makes concepts concrete.
