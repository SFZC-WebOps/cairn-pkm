# !sk — Skill Evidence Scraper

**Type:** Display | **Version:** 5.1

Extract professional skill evidence from conversations using the SFIA framework.

---

## Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `!sk` | Analyze current conversation | After technical discussions |
| `!sk-report` | Generate portfolio snapshot | Monthly/quarterly reviews |

---

## Workflow

1. Complete technical work in conversation
2. Type `!sk`
3. Copy output
4. Paste to skill-evidence.md

---

## !sk — Analyze Session

### Output Format

```markdown
### YYYY-MM-DD - [Session Topic]
**Source:** [Conversation context]
**Context:** [What was accomplished]
**Evidence Summary:** [One-line summary]

**Skills Identified:**
- SKILL Level N: [single-line evidence]
- SKILL Level N: [single-line evidence]

**Total Evidence:** N entries
**Confidence Distribution:** HIGH: N, MEDIUM: N
```

### Level Determination

| Instance Count | Level |
|----------------|-------|
| 2-4 validated | Level 2 |
| 5-9 validated | Level 3 |
| 10-19 validated | Level 4 |
| 20+ validated | Level 5 |

### Special Achievements (Auto Level 4)

- 99.9%+ uptime
- AI/automation implementation
- $50k+ cost savings

---

## !sk-report — Portfolio Analysis

Comprehensive skill portfolio analysis.

### Output Format

```markdown
# Skill Portfolio Report
**Generated:** YYYY-MM-DD HH:MM
**Period Covered:** [first entry] to [last entry]
**Total Entries Analyzed:** N

## Executive Summary
[2-3 sentence overview]

## Current Skill Levels

### Level 5 Skills (Strategic Impact)
- **SKILL (Code) - Full Name:** N instances
  - Evidence: Nx Level 5, Nx Level 4...
  - Trend: Growing | Stable | Declining
  - Key achievements: [highlights]

### Level 4 Skills (Advanced)
[Similar format]

### Level 3 Skills (Practitioner)
[Similar format]

### Level 2 Skills (Developing)
[Similar format]

## Skill Diversity Analysis
- Total unique skills: N
- Primary areas: [Top 3]
- Emerging: [Last 90 days]

## Evidence Velocity
- Entries per month: [Average]
- Most productive period: [Range]

## Gap Analysis
**Skills Not Yet Demonstrated:**
- SKILL: [Why valuable]

## Progression Recommendations
[Strategic suggestions]
```

---

## SFIA Skills Tracked

### Core Technical

| Code | Skill | Description |
|------|-------|-------------|
| PROG | Programming | Software development |
| ITOP | IT Operations | Infrastructure management |
| SCAD | Security Admin | Security provision |
| WEBA | Web Architecture | Web system design |
| DLMG | Data Management | Data asset management |
| CFMG | Configuration | System configuration |

### Design & UX

| Code | Skill | Description |
|------|-------|-------------|
| DESN | Systems Design | UI/UX design |
| USEV | UX Evaluation | Usability assessment |
| HCEV | Human Factors | Accessibility |
| GMDS | Graphic Design | Visual content |

### Project & Process

| Code | Skill | Description |
|------|-------|-------------|
| PRMG | Project Management | Project delivery |
| REQM | Requirements | Needs analysis |
| RLMT | Relationship Mgmt | Stakeholder relations |
| BPRE | Process Engineering | Workflow improvement |

### AI/ML & Emerging

| Code | Skill | Description |
|------|-------|-------------|
| ARTB | AI/ML | AI implementation |
| DTAN | Data Analytics | Data analysis |
| EMRG | Emerging Tech | Innovation |
| INCA | Innovation | New approaches |

---

## Detection Patterns

### Keyword + Action

When an action verb appears with a technical keyword:

```
"configured Drupal" → WEBA Level 3
"maintained 99.9% uptime" → ITOP Level 4
"secured SSL certificates" → SCAD Level 3
```

### Complexity Indicators

| Level | Keywords |
|-------|----------|
| Level 2 | basic, simple, routine, assisted |
| Level 3 | independently, resolved, managed |
| Level 4 | architected, designed, led, strategic |
| Level 5 | transformed, established, governed |

### Automatic Triggers

| Achievement | Result |
|-------------|--------|
| "99.9% uptime" | ITOP/SLMO Level 4 |
| "AI pilot program" | INCA Level 4 |
| "$50,000+ savings" | FMIT Level 4 |
| "Drupal 10 migration" | WEBA Level 4 |

---

## Confidence Scoring

### HIGH Confidence

- Clear action + outcome
- Measurable metrics
- Independent work
- Production impact
- Strategic results

### MEDIUM Confidence

- Standard task completion
- Some assistance
- Testing/staging work
- Process improvements

### LOW Confidence

- Vague descriptions
- Heavy assistance
- No clear outcome

---

## File Locations

| Purpose | Path |
|---------|------|
| Evidence log | `_local/data/skill-tracker/skill-evidence.md` |
| Skills registry | `_cairn-pkm/tools/skill-tracker/skills-registry.md` |
| Detection patterns | `_cairn-pkm/tools/skill-tracker/detection-patterns.md` |

---

## Operational Rules

1. **NO file writes** — Display only
2. **Single-line evidence** — Always
3. **Include confidence** — Always
4. **Read actual files** — Don't work from memory

---

*Back to [Commands Index](index.md) | See also: [Skill Tracker Tool](../tools/skill-tracker.md)*
