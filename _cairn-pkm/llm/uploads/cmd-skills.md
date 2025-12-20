# !skills - Skill Evidence Scraper
*Type: Display/Write | Version: 0.8.0 | Updated: 2025-12-19*

## Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `!skills` | Analyze current conversation | After technical discussions |
| `!skills-report` | Generate portfolio snapshot | Monthly/quarterly, before reviews |

**Workflow:** Technical work → `!skills` → Copy output → Paste to skill-evidence.md

**Location:** `{VAULT_PATH}/System/tools/professional-development/`

**Note:** Uses SFIA framework by default; adaptable to other competency frameworks.

---

## !skills — Analyze Session

### Process

1. Scan conversation for technical activities
2. Match against skill patterns from skills-registry.md
3. Apply level determination rules
4. Generate evidence summary

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
| 10-19 validated OR special achievement | Level 4 |
| 20+ validated OR transformational impact | Level 5 |

**Special Achievements (Auto Level 4):** High uptime (99.9%+), AI/automation implementation, significant cost savings ($50k+)

---

## !skills-report — Portfolio Analysis

Comprehensive skill portfolio analysis across all documented evidence.

### Process

1. Parse all entries in skill-evidence.md
2. Count instances per skill
3. Apply accumulation rules for achieved level
4. Group by level, calculate trends
5. Identify gaps, generate recommendations

### Output Format

```markdown
# Skill Portfolio Report
**Generated:** YYYY-MM-DD HH:MM
**Period Covered:** [First entry] to [Last entry]
**Total Entries Analyzed:** N

## Executive Summary
[2-3 sentence overview]

## Current Skill Levels

### Level 5 Skills (Strategic Impact)
- **SKILL (Code) - Full Name:** N total instances
  - Evidence: Nx Level 5, Nx Level 4, Nx Level 3
  - Trend: [Growing|Stable|Declining]
  - Key achievements: [Top 2-3 highlights]

[Repeat for Level 4, 3, 2]

## Skill Diversity Analysis
- Total unique skills demonstrated: N
- Primary skill areas: [Top 3]
- Emerging skill areas: [Last 90 days]

## Evidence Velocity
- Entries per month: [Average]
- Recent activity: [Last 30 days]

## Gap Analysis
**Skills Not Yet Demonstrated:**
- SKILL: [Why valuable]

## Progression Recommendations
[Strategic suggestions]
```

---

## Skill Detection Patterns

### Keyword + Action Combinations

- "configured [technology]" → relevant skill Level 3
- "maintained 99.9% uptime" → operations skill Level 4
- "secured [system]" → security skill Level 3

### Complexity Indicators

| Level | Keywords |
|-------|----------|
| Level 2 | basic, simple, routine, assisted |
| Level 3 | independently, resolved, managed |
| Level 4 | architected, designed, led, strategic |
| Level 5 | transformed, established, governed |

### Confidence Scoring

| Confidence | Criteria |
|------------|----------|
| HIGH | Clear action + measurable outcome, quantifiable metrics, independent production work |
| MEDIUM | Standard task completion, some collaboration, staging environment work |
| LOW | Vague descriptions, heavy assistance required, no clear outcome |

---

## Operational Rules

1. Execute immediately (no permission gates)
2. Use single-line evidence format
3. Always include confidence distribution
4. Read actual files (don't work from memory)
5. skill-evidence.md is append-only (user pastes after review)

**Complete:** Per `cmd-shared-patterns.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.8.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.7.1 | 2025-12-19 | Renamed from !sk to !skills |
| 0.7.0 | 2025-12-19 | Reset to pre-release versioning |
