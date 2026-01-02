# Cairn-PKM Specification Changes Log

*Audit trail documenting the pivot from menu-driven to conversational paradigm.*

*Date: 2025-01-01*

---

## Summary

Over two extended sessions, Cairn-PKM's LLM command specifications were fundamentally rewritten from a menu-driven approach to a conversational paradigm. This document captures why, how, and what changed.

---

## The Original Approach

### Menu-Driven Design

Commands presented numbered menus:

```
🧭 What would you like to do?

1. Open a project or area
2. Create something new
3. Edit something
4. Capture something
5. Record skill evidence
6. Close session

Enter number or describe what you need:
```

Each command had explicit flows with rigid phases.

### Problems Identified

1. **Friction** — Users had to navigate menus instead of just saying what they wanted
2. **Unnatural** — Real conversations don't work like numbered menus
3. **Rigid** — Hard to handle edge cases or mid-task questions
4. **LLM-unfriendly** — Fighting against LLM's natural conversational strengths

---

## The Pivot

### Trigger

User feedback during specification review: "This feels like a phone tree, not a conversation."

### Core Insight

LLMs are naturally conversational. The specifications were forcing them into a menu-processor role, which:
- Required more prompting
- Felt awkward
- Missed opportunities for natural interaction

### The Mantra

Three principles emerged:

1. **Context discovery through conversation** — Ask, don't assume
2. **Minimal friction** — Reasonable defaults, easy overrides  
3. **Natural flow** — Follow the user's lead, not a script

---

## Implementation Sessions

### Session 1: Full Rewrite

**Work done:**
- Rewrote all 11 command specifications
- Replaced menus with conversational flows
- Added "The Mantra" and "Interaction Model" to cmd-shared-patterns.md
- Conducted naive user simulation (14 turns)

**Files modified:**
- cmd-hi.md
- cmd-bye.md
- cmd-create.md
- cmd-edit.md
- cmd-capture.md
- cmd-changelog.md
- cmd-skills.md
- cmd-tour.md
- cmd-help.md
- cmd-setup.md
- cmd-shared-patterns.md
- cmd-output-behavior.md

**Simulation results:**
- Natural language worked throughout
- Menus only shown when explicitly helpful
- Edge cases surfaced 5 open questions

### Session 2: Clarifications and Edge Cases

**Open questions answered by user:**

#### 1. Cold Start Behavior

**Question:** When user just says "hi" with no context, what does LLM do?

**Answer:** Offer tour option:
> "Are you working on something, or would you like a tour of Cairn-PKM?"

After tour:
> "Want to start tracking a project, or is there something I should explain more?"

#### 2. Subtask Generation

**Question:** Should LLM auto-generate subtasks, or ask first?

**Answer:** Ask first, allow modification:
1. Offer: "Want me to suggest some subtasks?"
2. If yes: Show suggestions
3. User can accept/modify/skip
4. Example: "remove the backup one, I already did that"

**Future idea:** Pattern library by task type (not implemented yet)

#### 3. Project Numbering

**Question:** Auto-increment project numbers, or ask user?

**Answer:** Ask user:
> "What project number do you want? (You have p001 through p003 already, but you can pick any number)"

**Rationale:** Users may have non-linear schemes or reserved ranges.

#### 4. Session Log Output

**Question:** Where do session logs go?

**Answer:** No separate session log file.

- Task history entries → individual task files
- Project log entries → project home docs
- Capture/ is ONLY for notes and links (spec had this wrong)

**Flow:**
1. Display summary
2. Offer to write (if filesystem) or download
3. User places content

#### 5. Context Memory

**Question:** How to handle cross-session context?

**Answer:** End sessions with:
> "Next time, just tell me 'the widget launch' and I'll pick up where we left off."

Leverage LLM memory features where available.

---

## Simulations Run

### New User Simulation (9 turns)

Tested cold start through first project and task creation.

**Checkpoints passed:**
- ✓ Cold start offers tour
- ✓ Explains system when asked
- ✓ Project number asks user
- ✓ Subtasks ask before suggesting
- ✓ User can modify subtasks
- ✓ Session close is natural
- ✓ Log entry goes to project home (not separate file)
- ✓ No Capture/ for session logs
- ✓ Remembers context for next time

### Edge Case Simulation (15 turns)

Tested complex scenarios:
- Non-sequential project numbering (p001, p005, p010 → user picks p003)
- Ambiguous reference ("the tax thing" → found 2 matches)
- Context switch mid-session
- RC principle enforcement
- Cascading task updates
- Multi-track session summary

**All edge cases handled correctly.**

### Help Mid-Task Simulation (11 turns)

Tested user asking questions during task creation:
- "what's phase mean?"
- "what about viz?"
- "what's the difference between viz and priority?"
- "how do subtasks work?"

**Key finding:** System maintained draft context through 4 help questions, then resumed seamlessly.

### User Changes Mind Simulation (12 turns)

Tested abandonment scenarios:
- Started creating area, reconsidered, said "never mind"
- Started marking task complete, said "wait no"
- Pivoted to different project

**Key finding:** No guilt-tripping, clean recovery, accurate session summary.

### Malformed File Simulation (12 turns)

Tested error handling:
- Task list with one broken file (missing quote)
- User requested fix
- User requested scan for other problems
- Found second issue (bad indentation + incomplete email)

**Key finding:** Graceful degradation, clear diagnosis, user-controlled fixes.

---

## Files Modified (Final State)

| File | Changes |
|------|---------|
| cmd-hi.md | Cold start offers tour; removed menu-first approach |
| cmd-bye.md | Removed session log file concept; entries go to task files and project homes |
| cmd-create.md | Project number asks user; subtask suggestions ask first and allow modification |
| cmd-edit.md | Natural language changes; help mid-task preserves context |
| cmd-shared-patterns.md | Added The Mantra and Interaction Model sections |
| cmd-output-behavior.md | Fixed encoding issues |
| cmd-capture.md | Clarified Capture/ is only for notes/links |
| _INSTALLATION.md | Fixed encoding issues |

---

## Key Decisions Made

| Decision | Rationale |
|----------|-----------|
| LLM asks, user confirms | Natural conversation flow |
| No auto-increment project numbers | Users have their own schemes |
| Subtasks ask before suggesting | User agency over automation |
| No separate session log file | Distribute to where content belongs |
| Cold start offers tour | Helps new users without blocking experienced ones |
| Help mid-task preserves context | Don't lose work when user asks questions |
| "never mind" has no guilt | Abandonment is valid |
| Malformed files don't block | Show warning, continue with what works |

---

## Principles Documented

### Final Unified Principles

The Apollo robustness principles (VBM, GFC, RC) and the conversational interaction principles were merged into four unified principles:

1. **Ask → Show → Confirm** — Discover context, display draft, verify before write (combines VBM + context discovery)
2. **Minimal friction** — Sensible defaults, easy overrides, no unnecessary questions
3. **Follow the user** — Their language, their pace, "never mind" is valid (combines natural flow)
4. **Capture the why** — Ask for rationale, log reasons not just actions (RC, now conversational)

**What happened to Apollo:**
- **VBM** → Absorbed into "Ask → Show → Confirm" (show draft = verification)
- **GFC** → Moved to `cmd-output-behavior.md` as implementation detail (infrastructure, not interaction principle)
- **RC** → Became "Capture the why" with conversational prompts ("Why is this done?")

---

## Open Items for Future

1. **Subtask pattern library** — Common patterns by task type
2. **Cross-track operations** — Multiple projects in one session
3. **Batch operations** — "Mark all December tasks complete"
4. **Templates from examples** — "Create a task like the SSL one"
5. **LLM memory integration** — Persistent context across sessions

---

## How to Continue This Work

### To implement specs:
1. Read CONVERSATIONAL-SPEC.md for paradigm overview
2. Read individual cmd-*.md files for implementation details
3. Simulation transcripts in /mnt/transcripts/ show expected behavior

### To refine specs further:
1. Review edge case simulations for gaps
2. Run additional simulations for untested scenarios
3. Update CONVERSATIONAL-SPEC.md as authoritative source

### To test:
1. Start fresh conversation with cold start
2. Try natural language throughout
3. Test edge cases: help mid-task, abandonment, malformed files
4. Verify session close distributes content correctly

---

## Transcript Reference

Full conversation history available at:
`/mnt/transcripts/2026-01-01-05-39-53-conversational-spec-refinement.txt`

Previous session transcript referenced in compaction summary.

---

*This document is the audit trail. For the authoritative spec, see CONVERSATIONAL-SPEC.md.*
