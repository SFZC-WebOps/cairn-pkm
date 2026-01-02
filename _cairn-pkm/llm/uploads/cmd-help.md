# Getting Help
*Type: Display | Updated: 2025-01-01*

## Overview

Users ask for help naturally. The system explains what it can do and how things work, in context.

**Shortcut:** `!help` to see capabilities overview

---

## Natural Help Requests

### "What can you do?"

```
LLM: I can help you organize and track your work:

     **Track projects and areas**
     See what's happening, update progress, add log entries
     
     **Create and manage tasks**
     Create tasks, mark them complete, set priorities and due dates
     
     **Capture quick notes**
     Save ideas, links, or things to process later
     
     **Plan capacity**
     See how your time is allocated across projects
     
     **Document changes**
     Create changelogs when you make system changes
     
     What would you like to do?
```

### "How does [X] work?"

Answer in context:

```
User: How do tasks work?

LLM: A task is a work item that lives in a project or area.

     It's a markdown file with some metadata at the top:
     - Title, status, priority, due date
     - Assignment, phase, effort estimate
     
     And some sections in the body:
     - Task History (audit trail of changes)
     - Subtasks (checklist items)
     - Notes (free-form text)
     
     Want me to show you one from your vault?
```

### "I don't understand [X]"

Explain simply, offer examples:

```
User: I don't understand the difference between areas and projects

LLM: The difference is whether it ends:

     **Areas** are ongoing — they don't finish.
     Like "finance" (you always have finances to manage)
     or "health" (you always have health to track)
     
     **Projects** are temporary — they have a goal and end.
     Like "website-redesign" (done when the site launches)
     or "Q1-budget" (done when Q1 is planned)
     
     When a project finishes, it gets archived.
     Areas keep going forever.
```

---

## Shortcut: !help

Shows a concise capabilities overview:

```
LLM: Here's what I can help with:

     **Work with what you have**
     - Show project/area status → "show me [project]" or !hi-[track]
     - Edit tasks → "mark [task] complete" or !edit [file]
     - Update progress → "update progress to 80%"
     
     **Create new things**
     - Projects → "I need to track [something]" or !create project
     - Tasks → "I need to [do something]" or !create task
     - Areas → "I want to track [ongoing domain]" or !create area
     
     **Other tools**
     - Capture notes → !capture
     - Plan capacity → !capacity  
     - Document changes → !changelog
     - Track skills → !skills
     
     **Session management**
     - Start working → just tell me what you're working on
     - Wrap up → "I'm done" or !bye
     
     What do you need?
```

---

## Context-Aware Help

When user asks for help during a task, respond in context:

```
[User is editing a task]

User: help

LLM: You're editing 20250101-ssl-cert-update.md. You can:

     - Change status → "mark it complete" / "set to blocked"
     - Change priority → "make it critical"
     - Set due date → "due Friday" / "due January 15"
     - Add subtask → "add subtask: test the renewal"
     - Add note → "add note: waiting on credentials"
     - Add history → "add history: started working on this"
     
     Or just tell me what you want to change.
```

---

## Teaching Through Help

Help isn't just answering questions — it's explaining *why*:

```
User: What's effort_percent?

LLM: It's how much of your capacity a project takes — as a percentage.

     If something takes roughly a day a week, that's 20%.
     If it's your main focus, maybe 50%.
     A small maintenance task might be 5%.
     
     The !capacity command adds these up to show if you're
     overcommitted. Over 100% means too much on your plate.
     
     Your p014-site-rdsg is currently at 25%. Want to change it?
```

---

## Error Recovery

When user tries something that doesn't work:

```
User: !frobnicate

LLM: I don't have a frobnicate command. 
     
     Were you trying to:
     - Create something? → "create [type]"
     - Edit something? → "edit [name]"
     - See status? → "show me [project]"
     
     Or tell me what you're trying to do and I'll help.
```

---

## Shortcut Reference

For experienced users who want the quick list:

| Shortcut | What It Does |
|----------|--------------|
| `!hi` | Start session / show active tracks |
| `!hi-[track]` | Show specific track |
| `!create [type]` | Create area/project/task/object |
| `!edit [file]` | Edit specific file |
| `!capture` | Save quick note |
| `!capacity` | Show capacity analysis |
| `!capacity plan` | Capacity simulator |
| `!changelog` | Document a change |
| `!skills` | Record skill evidence |
| `!bye` | Close session with logging |
| `!tour` | Learn how system works |
