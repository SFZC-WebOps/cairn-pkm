# !capacity - Capacity Analysis View
*Type: Display | Updated: 2025-12-31*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!capacity` | Show current year capacity overview |
| `!capacity [year]` | Show specific year |
| `!capacity [Q1\|Q2\|Q3\|Q4]` | Show specific quarter detail |
| `!capacity stakeholder` | Group by stakeholder |
| `!capacity conflicts` | Show only overcommitted periods |
| `!capacity plan` | Capacity simulator (conversational what-if) |
| `!capacity report` | Generate downloadable report for leadership |

**Purpose:** Visualize time commitments across projects to identify overcommitment periods and support planning conversations.

**Design principle:** Always show project names and titles, not just numbers. Numbers are meaningless without knowing what's behind them.

**Output modes:**
- Default commands → Display in conversation (read-only analysis)
- `!capacity plan` → Capacity simulator (conversational what-if, nothing commits until you decide)
- `!capacity report` → Creates downloadable markdown file (for sharing)

---

## Initialization

Per `cmd-shared-patterns.md`

---

## Data Collection

```
SCAN: {VAULT_PATH}/Tracks/p###-*/
FOR EACH project folder:
  READ: _p###-*-home.md
  EXTRACT frontmatter:
    - project_id
    - title
    - summary              # Editorial insight on current state
    - status (skip if complete/archived)
    - start_date
    - end_date
    - effort_percent
    - peak_start
    - peak_end
    - stakeholder
    - category
    - flexibility
    - recurrence

FILTER: status IN (planning, active, onhold)
```

**Note on summary field:** This is free text editorial content describing what's actually happening with the project. It provides context that numbers alone can't convey — "Auditors arriving Monday" tells you more than "35% effort" about why April is critical.

---

## !capacity — Annual Overview

### Output Format

```
📊 CAPACITY ANALYSIS — {YEAR}
═══════════════════════════════════════════════════════════════

Generated: {Month DD, YYYY} at {HH:MM} {TIMEZONE}
Active projects: {N}

## Project Overview

| Project | Title | Effort | Dates | Stakeholder | Flex | Status |
|---------|-------|--------|-------|-------------|------|--------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | Mar–May | Finance | fixed | Auditors on-site week of Apr 14 |
| p002-site-rdsg | Website Redesign | 25% | Jan–Jun | Martinez | negot | Dev sprint 3, on track |
| p003-staf-trng | Staff Training Program | 20% | Feb–Jul | HR | negot | Cohort 3 starting, materials ready |
| p004-infr-upgr | Server Infrastructure Upgrade | 30% | May–Aug | IT | flex | Vendor confirmed, waiting to start |
| p005-annl-conf | Annual User Conference | 20% | Feb–May | Martinez | fixed | 500 registered, venue locked |
| p006-proc-docs | Process Documentation | 10% | Jan–Dec | self | flex | Background maintenance |
| p007-budg-plan | FY26 Budget Planning | 15% | Apr–Jun | Finance | negot | Initial drafts in review |

**Note:** Status column shows each project's `summary` field — the editorial insight on current state.

## Monthly View

### {Month}: {total}% {status_icon}
| Project | Effort | Status |
|---------|--------|--------|
| p001-cmpl-audt | 35% | Auditors on-site week of Apr 14 |
| p002-site-rdsg | 25% | Dev sprint 3, on track |
| **Total** | **{sum}%** | |

---

### {Month}: {base}% → {peak}% with peaks {status_icon}
| Project | Base | Peak | Status |
|---------|------|------|--------|
| p001-cmpl-audt | 35% | **52%** | Auditors on-site week of Apr 14 — **PEAK** |
| p002-site-rdsg | 25% | 25% | Dev sprint 3, on track |
| **Total** | **{base}%** | **{peak}%** | |

**What's driving this:** {narrative explanation of convergence}

**Flexibility analysis:**
- 🔒 Fixed ({N}%): {project list} — cannot move
- 🔄 Negotiable ({N}%): {project list}
- ⏸️ Flexible ({N}%): {project list}

---

[Repeat for each month, showing all active projects]

## Stakeholder Pressure Map

### {Stakeholder}: {total}% of your year
| Project | Effort | Period | Flexibility |
|---------|--------|--------|-------------|
| p001-cmpl-audt | 35% | Mar–May | 🔒 Fixed |
| p007-budg-plan | 15% | Apr–Jun | 🔄 Negotiable |

[Repeat for each stakeholder]

---

## Resolution Options for {Problem Period}

**The problem:** {base}% base load, {peak}% with peaks

**Option 1: {Action}** (easiest)
- {What to do with which project}
- Saves: {N}% in {months}
- Result: {new total}% (description)
- Trade-off: {consequence}

**Option 2: {Action}**
- ...

**Recommended combination:** {Options}
- {Summary of combined effect}
- {Resulting capacity}

═══════════════════════════════════════════════════════════════
```

---

## !capacity [quarter] — Quarter Detail

### Output Format

```
📊 CAPACITY DETAIL — Q2 2025
═══════════════════════════════════════════════════════════════

Period: Apr 1 – Jun 30
Status: 🔴 OVERCOMMITTED

## Projects Active This Quarter

| Project | Title | Effort | Peak | Stakeholder | Flex |
|---------|-------|--------|------|-------------|------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | 52% Apr | Finance | fixed |
| p002-site-rdsg | Website Redesign | 25% | 37% Apr–May | Martinez | negot |
| p003-staf-trng | Staff Training Program | 20% | — | HR | negot |
| p004-infr-upgr | Server Infrastructure Upgrade | 30% | 45% Jun | IT | flex |
| p005-annl-conf | Annual User Conference | 20% | 30% May | Martinez | fixed |
| p006-proc-docs | Process Documentation | 10% | — | self | flex |
| p007-budg-plan | FY26 Budget Planning | 15% | 22% May–Jun | Finance | negot |

## Week-by-Week View

### April
| Week | Dates | Total | Projects Active |
|------|-------|-------|-----------------|
| W14 | Mar 31–Apr 6 | 145% | Audit(35), Website(25), Training(20), Conference(20), Docs(10), Budget(15), Infra(30) |
| W15 | Apr 7–13 | 165% | + Audit peak starts (+17) |
| W16 | Apr 14–20 | 177% | + Website peak starts (+12) |
| W17 | Apr 21–27 | 177% | All peaks continuing |
| W18 | Apr 28–May 4 | 184% | + Conference peak starts (+10) |

### May
| Week | Dates | Total | Projects Active |
|------|-------|-------|-----------------|
| W19 | May 5–11 | 184% | Audit wrapping, Conference peak, Website peak |
| W20 | May 12–18 | 184% | **CONFERENCE WEEK (May 15–17)** |
| W21 | May 19–25 | 162% | Conference ends, Audit ends |
| W22 | May 26–Jun 1 | 137% | Website ends |

### June
| Week | Dates | Total | Projects Active |
|------|-------|-------|-----------------|
| W23 | Jun 2–8 | 122% | Training(20), Infra(30→45 peak), Docs(10), Budget(15→22 peak) |
| W24 | Jun 9–15 | 122% | Infrastructure peak |
| W25 | Jun 16–22 | 107% | Infra peak continues |
| W26 | Jun 23–30 | 97% | Budget ends, Infra peak ends |

## Conflict Analysis

**Hard constraints (cannot move):**
| Project | Effort | Why Fixed |
|---------|--------|-----------|
| p001-cmpl-audt | 35% | Regulatory deadline, auditors scheduled |
| p005-annl-conf | 20% | Venue contract signed, 500 attendees registered |

**Moveable work:**
| Project | Effort | How Flexible |
|---------|--------|--------------|
| p002-site-rdsg | 25% | Could accelerate to finish April, or extend to Q3 |
| p003-staf-trng | 20% | Cohorts could be rescheduled |
| p004-infr-upgr | 30% | Start date flexible, vendor available Q3 |
| p006-proc-docs | 10% | Can pause anytime |
| p007-budg-plan | 15% | Finance prefers Q2 but could extend |

## Options to Resolve Q2

**Option 1: Defer Infrastructure to Q3**
- Move p004-infr-upgr: May → July start
- Impact: Apr–Jun drops by 30%
- April: 155% → 125%
- May: 155% → 125%
- Trade-off: IT project delayed 2 months

**Option 2: Pause Process Docs during crunch**
- Pause p006-proc-docs: Apr–May
- Impact: Saves 10%
- Minimal trade-off, easy win

**Option 3: Accelerate Website completion**
- Complete p002-site-rdsg by April 30
- Impact: May drops by 25%
- May: 155% → 130%
- Trade-off: Requires Martinez approval, compressed timeline

**Recommended:** Options 1 + 2
- Defer Infrastructure + Pause Docs
- April: 155% → 115%
- May: 155% → 115%
- June: 100% → 60%
- Result: Tight but manageable

═══════════════════════════════════════════════════════════════
```

---

## !capacity stakeholder — By Stakeholder

### Output Format

```
📊 CAPACITY BY STAKEHOLDER — 2025
═══════════════════════════════════════════════════════════════

## Summary

| Stakeholder | Projects | Total Effort | Fixed | Negotiable | Flexible |
|-------------|----------|--------------|-------|------------|----------|
| Finance     | 2        | 50%          | 35%   | 15%        | —        |
| Martinez    | 2        | 45%          | 20%   | 25%        | —        |
| IT          | 1        | 30%          | —     | —          | 30%      |
| HR          | 1        | 20%          | —     | 20%        | —        |
| self        | 1        | 10%          | —     | —          | 10%      |

---

## Finance (50% of your committed time)

| Project | Title | Effort | Period | Peak | Flex |
|---------|-------|--------|--------|------|------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | Mar–May | Apr (+17%) | 🔒 Fixed |
| p007-budg-plan | FY26 Budget Planning | 15% | Apr–Jun | May–Jun (+7%) | 🔄 Negotiable |

**Notes:**
- Audit is regulatory — cannot move
- Budget timing preferred but has some flexibility
- Both projects overlap in Apr–May, creating 50% Finance load during crunch

---

## Martinez (45% of your committed time)

| Project | Title | Effort | Period | Peak | Flex |
|---------|-------|--------|--------|------|------|
| p002-site-rdsg | Website Redesign | 25% | Jan–Jun | Apr–May (+12%) | 🔄 Negotiable |
| p005-annl-conf | Annual User Conference | 20% | Feb–May | May 1–20 (+10%) | 🔒 Fixed |

**Notes:**
- Conference dates locked (venue contract)
- Website timeline is negotiable — could accelerate or extend
- Both peak in late April / early May

---

## IT (30% of your committed time)

| Project | Title | Effort | Period | Peak | Flex |
|---------|-------|--------|--------|------|------|
| p004-infr-upgr | Server Infrastructure Upgrade | 30% | May–Aug | Jun–Jul (+15%) | ⏸️ Flexible |

**Notes:**
- Entirely flexible timing
- Vendor available whenever
- Best candidate to shift if Q2 is overloaded

---

## HR (20% of your committed time)

| Project | Title | Effort | Period | Peak | Flex |
|---------|-------|--------|--------|------|------|
| p003-staf-trng | Staff Training Program | 20% | Feb–Jul | Mar (+10%) | 🔄 Negotiable |

**Notes:**
- Cohort schedule can be adjusted
- Peak already passed (March)
- Relatively steady effort through Q2

---

## self (10% of your committed time)

| Project | Title | Effort | Period | Peak | Flex |
|---------|-------|--------|--------|------|------|
| p006-proc-docs | Process Documentation | 10% | Jan–Dec | — | ⏸️ Flexible |

**Notes:**
- Background project, can pause anytime
- First thing to pause during crunch periods

---

## Stakeholder Pressure Points

**April–May crunch by stakeholder:**

| Stakeholder | Effort | % of Crunch |
|-------------|--------|-------------|
| Finance     | 50%    | 32%         |
| Martinez    | 45%    | 29%         |
| IT          | 30%    | 19%         |
| HR          | 20%    | 13%         |
| self        | 10%    | 7%          |

**Talking point:** "Finance and Martinez together consume 61% of the April–May crunch. 
The audit is fixed, but I have flexibility on the website timeline if Martinez agrees."

═══════════════════════════════════════════════════════════════
```

---

## !capacity conflicts — Conflicts Only

### Output Format

```
📊 CAPACITY CONFLICTS — 2025
═══════════════════════════════════════════════════════════════

## Overcommitted Periods

### 🔴 April 2025: 155% base → 194% with peaks

**Projects contributing:**

| Project | Title | Base | Peak | Flex | Current Status |
|---------|-------|------|------|------|----------------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | 52% | 🔒 Fixed | Auditors on-site week of Apr 14 |
| p002-site-rdsg | Website Redesign | 25% | 37% | 🔄 Negot | Dev sprint 3, on track |
| p003-staf-trng | Staff Training Program | 20% | 20% | 🔄 Negot | Cohort 3 starting, materials ready |
| p004-infr-upgr | Server Infrastructure | 30% | 30% | ⏸️ Flex | Vendor confirmed, waiting to start |
| p005-annl-conf | Annual User Conference | 20% | 30% | 🔒 Fixed | 500 registered, venue locked |
| p006-proc-docs | Process Documentation | 10% | 10% | ⏸️ Flex | Background maintenance |
| p007-budg-plan | FY26 Budget Planning | 15% | 15% | 🔄 Negot | Initial drafts in review |

**Note:** "Current Status" shows each project's summary field — the real-time editorial insight.

**Flexibility breakdown:**
- 🔒 Fixed: 55% (Audit + Conference) — cannot move
- 🔄 Negotiable: 60% (Website + Training + Budget)
- ⏸️ Flexible: 40% (Infrastructure + Docs)

**Resolution options:**

| Option | Action | Saves | Result | Trade-off |
|--------|--------|-------|--------|-----------|
| 1 | Defer p004-infr-upgr to July | 30% | 125% base | IT delayed 2 months |
| 2 | Pause p006-proc-docs | 10% | 145% base | Background work paused |
| 3 | Accelerate p002-site-rdsg to finish April | 25% in May | 130% in May | Compressed timeline |
| 4 | Defer p003-staf-trng cohorts to Q3 | 20% | 135% base | HR timeline extends |

**Recommended:** Options 1 + 2 (Defer Infrastructure + Pause Docs)
- April: 155% → 115%
- Manageable with effort during peaks

---

### 🔴 May 2025: 155% base → 184% with peaks

**Projects contributing:**

| Project | Title | Base | Peak | Flex | Why Active |
|---------|-------|------|------|------|------------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | 35% | 🔒 Fixed | Wrapping up |
| p002-site-rdsg | Website Redesign | 25% | 37% | 🔄 Negot | Final development |
| p003-staf-trng | Staff Training Program | 20% | 20% | 🔄 Negot | Cohort 4 |
| p004-infr-upgr | Server Infrastructure | 30% | 30% | ⏸️ Flex | Migration prep |
| p005-annl-conf | Annual User Conference | 20% | 30% | 🔒 Fixed | **EVENT MAY 15-17** |
| p006-proc-docs | Process Documentation | 10% | 10% | ⏸️ Flex | Ongoing |
| p007-budg-plan | FY26 Budget Planning | 15% | 22% | 🔄 Negot | Deadline approaching |

**Key date:** Conference May 15–17 is immovable anchor

**Resolution options:**

Same as April, plus:
| Option | Action | Saves | Result | Trade-off |
|--------|--------|-------|--------|-----------|
| 5 | Complete p001-cmpl-audt by May 10 | 35% mid-May | 120% late May | Push audit close |

---

### ⚠️ June 2025: 100% base → 122% with peaks

**Projects contributing:**

| Project | Title | Base | Peak | Flex | Why Active |
|---------|-------|------|------|------|------------|
| p002-site-rdsg | Website Redesign | 25% | 25% | 🔄 Negot | Final month |
| p003-staf-trng | Staff Training Program | 20% | 20% | 🔄 Negot | Cohort 5 |
| p004-infr-upgr | Server Infrastructure | 30% | 45% | ⏸️ Flex | **MIGRATION PEAK** |
| p006-proc-docs | Process Documentation | 10% | 10% | ⏸️ Flex | Ongoing |
| p007-budg-plan | FY26 Budget Planning | 15% | 22% | 🔄 Negot | **DEADLINE** |

**Notes:** 
- If Infrastructure deferred per April recommendation, June drops to 70% base
- Likely manageable even without changes — peaks are temporary

---

## Conflict Summary

| Period | Base | Peak | Severity | Fixed % | Moveable % |
|--------|------|------|----------|---------|------------|
| Feb | 110% | 120% | 🔴 Over | 55% | 55% |
| Mar | 125% | 152% | 🔴 Over | 55% | 70% |
| Apr | 155% | 194% | 🔴🔴 Critical | 55% | 100% |
| May | 155% | 184% | 🔴🔴 Critical | 55% | 100% |
| Jun | 100% | 122% | ⚠️ Peak risk | 0% | 100% |

**Key insight:** 55% is locked (Audit + Conference). Everything else is theoretically moveable.

**Simplest fix:** Defer Infrastructure (p004) to Q3
- Removes 30% from Apr–Jun
- IT project is fully flexible
- Drops critical months from 155% to 125%

═══════════════════════════════════════════════════════════════
```

---

## !capacity plan — Capacity Simulator

The LLM becomes a capacity simulator. It holds the model (all project metadata), you propose changes conversationally, it calculates the resulting state. Iterate until you find a workable configuration. Nothing commits to your vault until you decide.

**The conversation is the simulation interface.**

### Setup

Upload all project home documents to the chat, or use filesystem access. The LLM needs the frontmatter (effort_percent, dates, stakeholder, flexibility, summary) to run the simulation accurately.

### How It Works

1. **Load state** — LLM reads all project data, calculates baseline capacity
2. **Propose changes** — "What if we defer infrastructure to July?"
3. **See results** — LLM recalculates, shows before/after
4. **Iterate** — Try combinations, explore trade-offs
5. **Decide** — When you find a workable state, say what's approved
6. **Export** — Generate report with decisions and actions to apply

### Example

```
You: Let's simulate some capacity changes. I need to get Q2 under control.

LLM: [Loads model]
     
     Current state:
     - April: 155% (critical)
     - May: 155% (critical)
     - Fixed load: 55% (audit + conference)
     
     What would you like to try?

You: Move infrastructure to July.

LLM: [Recalculates]

     Simulated state:
     - April: 155% → 125%
     - May: 155% → 125%
     - July: 10% → 40%
     
     Infrastructure summary says "Vendor confirmed, waiting to start" 
     — no sunk cost.

You: Also pause documentation during the crunch.

LLM: [Recalculates]

     Simulated state:
     - April: 125% → 115%
     - May: 125% → 115%
     
     Combined effect: 155% → 115%. Manageable.

You: What's still driving 115%?

LLM: | Project | Effort | Status |
     |---------|--------|--------|
     | Audit | 35% | Auditors on-site week of Apr 14 |
     | Website | 25% | Dev sprint 3, on track |
     | Conference | 20% | 500 registered, venue locked |
     | Training | 20% | Cohort 3 starting |
     | Budget | 15% | Initial drafts in review |
     
     Audit and conference are fixed (55%). The rest is real work 
     with stakeholders expecting delivery.

You: OK, Martinez approved the infrastructure move. Let's lock that in.

LLM: ✓ Decision recorded: Infrastructure deferred to Q3 (Martinez approved)
     
     Current approved changes:
     1. p004-infr-upgr → July start
     2. p006-proc-docs → paused April-May
     
     Want me to generate the session report?
```

### What the Simulator Tracks

**Model state:**
- Baseline capacity (from vault data)
- Simulated changes (proposed but not committed)
- Resulting capacity (recalculated after each change)

**Session state:**
- Decisions made (who approved what)
- Action items (who does what next)
- Notes (context worth preserving)

### Ending the Session

Say "generate the report" or "what did we decide" or just "done."

The LLM produces a report capturing:
- Before/after capacity comparison
- Approved changes with rationale
- Decisions and who made them
- Action items to apply changes
- Commands to update your vault

### Session Report

**Filename:** `capacity-simulation-{YYYY-MM-DD}.md`

```markdown
# Capacity Planning Session

**Date:** December 31, 2025
**Duration:** 35 minutes

---

## Summary

Started with 2 critical periods (April–May at 155%).
Explored 3 scenarios, approved 2 changes.
Result: Critical periods reduced to 115%.

---

## Before & After

| Period | Before | After | Change |
|--------|--------|-------|--------|
| April | 155% 🔴 | 115% ✓ | -40% |
| May | 155% 🔴 | 115% ✓ | -40% |
| June | 100% | 70% | -30% |
| July | 10% | 40% | +30% |

---

## Approved Changes

### 1. Defer Infrastructure Upgrade
- **Project:** p004-infr-upgr
- **Change:** Start date May → July
- **Impact:** -30% in Apr–Jun
- **Approved by:** Martinez

### 2. Pause Documentation  
- **Project:** p006-proc-docs
- **Change:** Paused April–May
- **Impact:** -10% in Apr–May
- **Approved by:** Greg (internal project)

---

## Decisions

1. Martinez approved deferring infrastructure to Q3
2. Documentation pause approved (no stakeholder impact)

---

## Action Items

| Action | Owner |
|--------|-------|
| Update p004 timeline and notify IT vendor | Greg |
| Confirm training cohort 4 schedule | HR |

---

## Notes

- IT vendor prefers Q3 anyway
- HR offered to shift training cohort 4 as fallback option

---

## To Apply Changes

\`\`\`
!edit p004-infr-upgr
> field start_date 2025-07-01
> log decision Deferred to Q3 per planning session
> done

!edit p006-proc-docs  
> log decision Paused April-May per planning session
> done
\`\`\`

---

*Session generated from Cairn-PKM capacity planning*
```

### Why This Works

**Traditional tools:** You manipulate data, then interpret results.

**Capacity simulator:** You state intent, the model calculates, you react.

The LLM holds:
- All project metadata (effort, dates, flexibility, stakeholder, summary)
- Baseline capacity calculations
- Simulated deltas from proposed changes
- Cumulative state after each change

You don't need to know the formulas. You say "move this to July" and see what happens.

---

## !capacity report — Leadership Report

Creates a downloadable markdown file formatted for sharing with leadership. Removes interactive elements, adds executive summary, and uses professional tone.

### Prompts

```
Generating capacity report...

1. Report title? (default: "Capacity Planning Report")
2. Time period? (default: current year)
3. Include recommendations? (yes/no, default: yes)
4. Author name? (default: from user prefs)
```

### Output

**Filename:** `capacity-report-{YYYY-MM-DD}.md`
**Location:** Per `cmd-output-behavior.md`

### Report Template

```markdown
# {Report Title}

**Prepared by:** {Author}
**Date:** {YYYY-MM-DD}
**Period:** {Year or Quarter}

---

## Executive Summary

{Name} is currently managing {N} active projects representing {total}% of available capacity if run concurrently. 

**Key findings:**
- **Q1:** {status} — {base}% base capacity
- **Q2:** {status} — {base}% base capacity ({assessment})
- **Q3:** {status} — {base}% base capacity
- **Q4:** {status} — {base}% base capacity

**Critical periods identified:**
- {Month(s)}: {base}% committed ({peak}% during peaks)

**Primary constraint:** {description of fixed commitments}

**Recommended actions:**
1. {Action with quantified impact}
2. {Action with quantified impact}

---

## Current Project Portfolio

| Project | Description | Effort | Timeline | Owner | Flexibility |
|---------|-------------|--------|----------|-------|-------------|
| p001-cmpl-audt | Annual Compliance Audit | 35% | Mar–May | Finance | Fixed |
| p002-site-rdsg | Website Redesign | 25% | Jan–Jun | Martinez | Negotiable |
| p003-staf-trng | Staff Training Program | 20% | Feb–Jul | HR | Negotiable |
| p004-infr-upgr | Server Infrastructure Upgrade | 30% | May–Aug | IT | Flexible |
| p005-annl-conf | Annual User Conference | 20% | Feb–May | Martinez | Fixed |
| p006-proc-docs | Process Documentation Update | 10% | Jan–Dec | Internal | Flexible |
| p007-budg-plan | FY26 Budget Planning | 15% | Apr–Jun | Finance | Negotiable |

**Capacity by stakeholder:**

| Stakeholder | Projects | Total Commitment | Fixed | Negotiable |
|-------------|----------|------------------|-------|------------|
| Finance | 2 | 50% | 35% | 15% |
| Martinez | 2 | 45% | 20% | 25% |
| IT | 1 | 30% | — | 30% |
| HR | 1 | 20% | — | 20% |
| Internal | 1 | 10% | — | 10% |

---

## Capacity Analysis by Month

### Q1 Summary (Jan–Mar)

| Month | Capacity | Status | Key Drivers |
|-------|----------|--------|-------------|
| January | 75% | ✓ Normal | Website, Conference prep, Documentation |
| February | 110% | ⚠️ Over | + Compliance Audit begins, + Training launches |
| March | 125% | ⚠️ Over | + Budget Planning begins, Training peak |

### Q2 Summary (Apr–Jun) — CRITICAL PERIOD

| Month | Capacity | Status | Key Drivers |
|-------|----------|--------|-------------|
| April | 155% | 🔴 Critical | All projects active, Audit peak, Conference prep |
| May | 155% | 🔴 Critical | Conference event (May 15–17), Website deadline |
| June | 100% | ✓ Normal | Infrastructure migration, Budget deadline |

### Q3 Summary (Jul–Sep)

| Month | Capacity | Status | Key Drivers |
|-------|----------|--------|-------------|
| July | 60% | ✓ Normal | Training final cohort, Infrastructure |
| August | 40% | ✓ Light | Infrastructure completion |
| September | 10% | ✓ Light | Documentation only |

### Q4 Summary (Oct–Dec)

| Month | Capacity | Status | Key Drivers |
|-------|----------|--------|-------------|
| October | 10% | ✓ Light | Documentation only |
| November | 10% | ✓ Light | Documentation only |
| December | 10% | ✓ Light | Documentation only |

---

## Risk Assessment

### Overcommitment Risk: HIGH (April–May)

**Situation:** 155% capacity committed during April–May, with peaks reaching 184–194%.

**Impact if unchanged:**
- Increased risk of missed deadlines
- Quality degradation across deliverables
- Team burnout during sustained overcommitment

**Root cause:** Convergence of:
- Regulatory audit (fixed deadline)
- Annual conference (fixed event date)
- Multiple negotiable projects scheduled concurrently

### Constraint Analysis

**Fixed commitments (55% of Q2 load):**
| Project | Effort | Why Fixed |
|---------|--------|-----------|
| Annual Compliance Audit | 35% | Regulatory requirement, auditors scheduled |
| Annual User Conference | 20% | Venue contract, 500+ registered attendees |

**Negotiable commitments (60% of Q2 load):**
| Project | Effort | Flexibility |
|---------|--------|-------------|
| Website Redesign | 25% | Timeline could compress or extend |
| Staff Training | 20% | Cohorts can be rescheduled |
| Budget Planning | 15% | Preferred Q2 but can extend |

**Flexible commitments (40% of Q2 load):**
| Project | Effort | Flexibility |
|---------|--------|-------------|
| Infrastructure Upgrade | 30% | Start date fully flexible |
| Process Documentation | 10% | Can pause anytime |

---

## Recommendations

### Option A: Defer Infrastructure Upgrade (Recommended)

**Action:** Move Server Infrastructure Upgrade (p004) start date from May to July.

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| April capacity | 155% | 125% | -30% |
| May capacity | 155% | 125% | -30% |
| June capacity | 100% | 70% | -30% |

**Trade-offs:**
- IT project completion moves from August to October
- No impact on other stakeholders
- Vendor confirms Q3 availability

**Risk reduction:** High → Medium

### Option B: Compress Website Timeline

**Action:** Accelerate Website Redesign (p002) to complete by April 30.

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| May capacity | 155% | 130% | -25% |
| June capacity | 100% | 75% | -25% |

**Trade-offs:**
- Requires Martinez approval for accelerated timeline
- Development team sprint intensity increases in March–April
- Launch moves up by 2 months

**Risk reduction:** High → Medium-High

### Option C: Combined Approach (Maximum Relief)

**Action:** Defer Infrastructure (Option A) + Pause Documentation during April–May.

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| April capacity | 155% | 115% | -40% |
| May capacity | 155% | 115% | -40% |

**Trade-offs:**
- IT project delayed 2 months
- Documentation progress pauses 2 months
- No impact on external stakeholders

**Risk reduction:** High → Low-Medium

---

## Decision Required

To reduce Q2 capacity risk, leadership approval is requested for:

☐ **Option A:** Defer Infrastructure Upgrade to Q3
☐ **Option B:** Accelerate Website Redesign completion
☐ **Option C:** Combined approach (defer Infrastructure + pause Documentation)
☐ **Alternative:** ________________________________

**Decision needed by:** {date} (to allow project replanning)

---

## Appendix: Project Details

[Include detailed breakdown of each project if needed]

---

*Report generated from Cairn-PKM capacity analysis*
```

### Report Customization

**Tone adjustments:**
- Removes emoji status indicators (✓ → "Normal", 🔴 → "Critical")
- Uses "capacity" instead of "effort_percent"
- Frames as organizational resource planning
- Includes decision checkboxes for leadership

**Sections can be toggled:**
```
!capacity report --no-recommendations    # Skip recommendations section
!capacity report --summary-only          # Executive summary + portfolio only
!capacity report --quarter Q2            # Focus on single quarter
```

---

## Calculation Logic

### Monthly Effort Calculation

```
FOR EACH month in range:
  base_effort = 0
  peak_effort = 0
  
  FOR EACH project:
    IF month overlaps (start_date, end_date):
      base_effort += effort_percent
      
      IF month overlaps (peak_start, peak_end):
        # During peak, effort is higher
        # Assume peak = 1.5x base unless peak fields indicate otherwise
        peak_effort += effort_percent * 1.5
      ELSE:
        peak_effort += effort_percent
  
  STORE: month_data[month] = {base: base_effort, peak: peak_effort}
```

### Recurrence Handling

```
IF recurrence == "annual":
  # Project repeats every year in same period
  # Extend date range to all years in view
  
IF recurrence == "quarterly":
  # Project repeats each quarter
  # Apply effort_percent to each quarter
  
IF recurrence contains specific quarter (e.g., "Q4"):
  # Project only occurs in that quarter each year
```

### Status Thresholds

| Base % | Peak % | Status |
|--------|--------|--------|
| ≤85% | ≤100% | ✓ OK |
| 86-100% | ≤100% | ✓ OK (tight) |
| ≤100% | 101-120% | ⚠️ PEAK RISK |
| 101-120% | any | 🔴 OVER |
| >120% | any | 🔴🔴 CRITICAL |

---

## Edge Cases

### Missing Data

| Missing Field | Handling |
|---------------|----------|
| effort_percent | Skip project, warn: "p### missing effort data" |
| start_date | Use created date |
| end_date | Assume 3 months from start |
| peak_* | Ignore peaks, use base only |
| stakeholder | Group under "(unassigned)" |
| flexibility | Assume "negotiable" |

### Project Without Dates

```
IF no start_date AND no end_date:
  IF status == "active":
    # Assume current month through end of quarter
    start_date = first of current month
    end_date = end of current quarter
  ELSE:
    # Planning/onhold - skip from calculations
    WARN: "p### has no dates, excluded from timeline"
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No projects found | "No active projects with capacity data. Add effort_percent to project frontmatter." |
| No projects with dates | "Projects found but none have date ranges. Add start_date/end_date to see timeline." |
| Invalid quarter | "Invalid quarter. Use Q1, Q2, Q3, or Q4." |
| Year out of range | "No project data for {year}. Projects span {earliest} to {latest}." |
| Parse error | "Could not parse {project_id}: {error}. Skipping." |

Common errors: See `cmd-shared-patterns.md`

---

## Integration

| Works With | How |
|------------|-----|
| !hi | Capacity data visible when viewing projects |
| !create project | New projects include capacity fields |
| !edit | Update capacity fields on projects |
| !bye | Could note capacity impact of session work |

---

## Meeting Prep Usage

Before a planning meeting, run:

```
!capacity conflicts
```

This gives you:
1. **Specific periods** where you're overcommitted
2. **Which projects** are causing the conflict (with titles, not just IDs)
3. **Why each project is active** during that period
4. **What's fixed vs. moveable** with reasons
5. **Concrete options** with quantified impact

**Example talking points this generates:**

"Looking at April, I'm at 155% base capacity before any peaks hit. Here's what's driving it:

- The compliance audit (35%) and annual conference (20%) are fixed — that's 55% I can't move.
- The website redesign is 25%, infrastructure upgrade is 30%, and I have three other projects totaling 45%.

If we defer the infrastructure upgrade to Q3 — which IT says is fine — April drops from 155% to 125%. Still tight during the audit peak, but manageable.

The alternative is compressing the website timeline, but that needs Martinez to approve an accelerated schedule."

**Before vs. after the conversation:**

| Approach | What You Say |
|----------|--------------|
| Without data | "I'm really busy in April, might need to push some things" |
| With !capacity | "April is 155% committed. 55% is fixed (audit + conference). Deferring infrastructure saves 30%. Can we move it to Q3?" |

The second version gives your boss concrete information to work with and specific asks to approve or negotiate.
