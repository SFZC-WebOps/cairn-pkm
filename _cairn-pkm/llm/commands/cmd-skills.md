# !skills - Skill Evidence Scraper
*Type: Display/Write | Version: 0.7.1 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `!skills` | Analyze current conversation | After every technical discussion |
| `!skills-report` | Generate portfolio snapshot | Monthly/quarterly reviews, before performance reviews |

**Workflow:** Complete technical work Ã¢â€ â€™ Run `!skills` Ã¢â€ â€™ Copy output Ã¢â€ â€™ Paste to skill-evidence.md

---

## Requirements

**Assistant Capabilities:**
- Conversation context analysis
- Pattern matching against skill definitions
- Skill level determination

**Vault Structure:**
- `System/tools/professional-development/skills-registry.md` Ã¢â‚¬â€ Skill definitions
- `System/tools/professional-development/detection-patterns.md` Ã¢â‚¬â€ Pattern rules
- `System/tools/professional-development/skill-evidence.md` Ã¢â‚¬â€ Evidence log

**Note:** Uses SFIA (Skills Framework for the Information Age) by default, but can be adapted to other competency frameworks.

---

## !skills Ã¢â‚¬â€ Analyze Session

### Inputs
- Current conversation history
- `{VAULT_PATH}/System/tools/professional-development/skills-registry.md`
- `{VAULT_PATH}/System/tools/professional-development/detection-patterns.md`

### Process
1. Scan conversation for technical activities
2. Match against skill patterns
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

### Special Achievements (Auto Level 4)
- High uptime achievements (99.9%+)
- AI/automation implementation
- Significant cost savings ($50k+)

---

## !skills-report Ã¢â‚¬â€ Portfolio Analysis

### Purpose
Comprehensive skill portfolio analysis showing current standing across all skills.

### Inputs
- `{VAULT_PATH}/System/tools/professional-development/skill-evidence.md` (all entries)
- `{VAULT_PATH}/System/tools/professional-development/skills-registry.md` (definitions)
- `{VAULT_PATH}/System/tools/professional-development/detection-patterns.md` (pattern rules)

### Process
1. Parse all entries in skill-evidence.md
2. Extract every skill mention with level
3. Count instances per skill
4. Apply accumulation rules to determine current achieved level
5. Group skills by achieved level
6. Calculate progression metrics
7. Analyze trends
8. Identify gaps
9. Generate strategic recommendations

### Output Format
```markdown
# Skill Portfolio Report
**Generated:** YYYY-MM-DD HH:MM
**Period Covered:** [First entry date] to [Last entry date]
**Total Entries Analyzed:** N

## Executive Summary
[2-3 sentence overview of skill profile strength]

## Current Skill Levels

### Level 5 Skills (Strategic Impact)
- **SKILL (Code) - Full Name:** N total instances
  - Evidence: Nx Level 5, Nx Level 4, Nx Level 3
  - Trend: [Growing|Stable|Declining]
  - Key achievements: [Top 2-3 highlights]

### Level 4 Skills (Advanced)
[Similar format]

### Level 3 Skills (Practitioner)
[Similar format]

### Level 2 Skills (Developing)
[Similar format]

## Skill Diversity Analysis
- **Total unique skills demonstrated:** N
- **Primary skill areas:** [Top 3 categories]
- **Emerging skill areas:** [Skills added in last 90 days]

## Evidence Velocity
- **Entries per month:** [Average]
- **Most productive period:** [Date range]
- **Recent activity:** [Last 30 days]

## Gap Analysis
**Skills Not Yet Demonstrated:**
- **SKILL:** [Why valuable to develop]

## Progression Recommendations
[Strategic suggestions for skill development]

## Notes
- This report reflects documented evidence only
- Actual capabilities may exceed documented instances
```

---

## Skill Detection Patterns

### Pattern Recognition

**1. Keyword + Action Combinations**
- "configured [technology]" Ã¢â€ â€™ relevant skill Level 3
- "maintained 99.9% uptime" Ã¢â€ â€™ operations skill Level 4
- "secured [system]" Ã¢â€ â€™ security skill Level 3

**2. Complexity Indicators**
| Level | Keywords |
|-------|----------|
| Level 2 | basic, simple, routine, assisted |
| Level 3 | independently, resolved, managed |
| Level 4 | architected, designed, led, strategic |
| Level 5 | transformed, established, governed |

**3. Project Context**
Project/track identifiers indicate skill domains.

### Confidence Scoring

| Confidence | Criteria |
|------------|----------|
| HIGH | Clear action + measurable outcome, quantifiable metrics, independent work with production impact, strategic/transformational results |
| MEDIUM | Standard task completion, some assistance or collaboration, testing/staging environment work, process improvements without metrics |
| LOW | Vague descriptions without specifics, heavy assistance required, no clear outcome demonstrated |

---

## Operational Rules

1. **Output per user preferences** â€” Use OUTPUT_FILE pattern from cmd-output-behavior.md
2. **NO permission gates** â€” Commands execute immediately
3. **ALWAYS use single-line evidence format** for !skills output
4. **ALWAYS include confidence distribution** in output
5. **ALWAYS stop after completion pattern**
6. **Read actual files** â€” Don't work from memory, use filesystem

**Note:** While !skills generates output per user preferences, the skill-evidence.md file is append-only and should be updated manually by the user after reviewing the generated evidence.

---
## Completion Pattern

```
Ã¢Å“â€œ Analysis complete - N evidence items validated
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

---

## File Paths

| Purpose | Path |
|---------|------|
| Base | `{VAULT_PATH}/System/tools/professional-development/` |
| Evidence log | `skill-evidence.md` (append-only via manual paste) |
| Skill definitions | `skills-registry.md` |
| Pattern rules | `detection-patterns.md` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.7.1 | 2025-12-19 | Renamed command from !sk to !skills for clarity |
| 0.7.0 | 2025-12-19 | Reset to pre-release versioning (was 5.1) |
| 5.1 | 2025-12-16 | Standardized format |
| 5.0 | 2025-12-15 | LLM-agnostic refactor |
| 4.0 | 2025-11-07 | Previous version |
