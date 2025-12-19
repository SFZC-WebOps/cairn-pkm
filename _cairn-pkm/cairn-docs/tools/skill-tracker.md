# Skill Tracker Tool

Document professional skills using the SFIA framework.

---

## Purpose

- Track skill evidence from work
- Build a professional portfolio
- Support career development
- Document competency levels

---

## File Locations

| Purpose | Path |
|---------|------|
| Skills registry | `_cairn-pkm/tools/skill-tracker/skills-registry.md` |
| Detection patterns | `_cairn-pkm/tools/skill-tracker/detection-patterns.md` |
| Your evidence | `_local/data/skill-tracker/skill-evidence.md` |

---

## Usage

### After Technical Work

```
!sk
```

Analyzes conversation and extracts skill evidence.

### For Portfolio Review

```
!sk-report
```

Generates comprehensive skill portfolio analysis.

See [!sk command](../commands/cmd-sk.md) for detailed usage.

---

## SFIA Framework

Skills Framework for the Information Age (SFIA) is an industry-standard competency framework.

### Level Definitions

| Level | Name | Description |
|-------|------|-------------|
| 1 | Follow | Works under supervision |
| 2 | Assist | Helps with tasks |
| 3 | Apply | Works independently |
| 4 | Enable | Enables others, designs solutions |
| 5 | Ensure | Strategic oversight |
| 6 | Initiate | Transformational leadership |

---

## Skills Tracked

### Core Technical

| Code | Skill |
|------|-------|
| PROG | Programming/Software Development |
| ITOP | IT Operations Management |
| SCAD | Security Administration |
| WEBA | Web Architecture |
| DLMG | Data Management |
| CFMG | Configuration Management |

### Design & UX

| Code | Skill |
|------|-------|
| DESN | Systems Design |
| USEV | User Experience Evaluation |
| HCEV | Human Factors Integration |
| GMDS | Graphic Design |

### Project & Process

| Code | Skill |
|------|-------|
| PRMG | Project Management |
| REQM | Requirements Definition |
| RLMT | Relationship Management |
| BPRE | Business Process Re-engineering |

### AI/ML & Emerging

| Code | Skill |
|------|-------|
| ARTB | Artificial Intelligence/ML |
| DTAN | Data Analytics |
| EMRG | Emerging Technology |
| INCA | Innovation |

### Operations & Service

| Code | Skill |
|------|-------|
| SLMO | Service Level Management |
| PBMG | Problem Management |
| CHMG | Change Management |
| SINT | Systems Integration |

---

## Detection Patterns

### Keyword + Action

```
"configured Drupal" → WEBA Level 3
"maintained 99.9% uptime" → ITOP Level 4
"secured SSL certificates" → SCAD Level 3
```

### Complexity Indicators

| Keywords | Level |
|----------|-------|
| basic, simple, routine | 2 |
| independently, resolved | 3 |
| architected, designed, led | 4 |
| transformed, established | 5 |

### Automatic Level 4 Triggers

- 99.9%+ uptime
- AI pilot program
- $50,000+ savings
- Major platform migration

---

## Confidence Scoring

### HIGH

- Clear action + outcome
- Measurable metrics
- Independent work
- Production impact

### MEDIUM

- Standard task completion
- Some assistance
- Testing/staging work

### LOW

- Vague descriptions
- Heavy assistance
- No clear outcome

---

## Level Accumulation

| Instance Count | Achieved Level |
|----------------|----------------|
| 2-4 validated | Level 2 |
| 5-9 validated | Level 3 |
| 10-19 validated | Level 4 |
| 20+ validated | Level 5 |

---

## Evidence Entry Format

```markdown
### YYYY-MM-DD - [Topic]
**Source:** [Context]
**Context:** [What was done]
**Evidence Summary:** [One line]

**Skills Identified:**
- SKILL Level N: [single-line evidence]
- SKILL Level N: [single-line evidence]

**Total Evidence:** N entries
**Confidence Distribution:** HIGH: N, MEDIUM: N
```

---

## Portfolio Report Sections

1. **Executive Summary** — Overall skill profile
2. **Current Skill Levels** — By level (5 → 2)
3. **Skill Diversity** — Breadth of coverage
4. **Evidence Velocity** — Activity over time
5. **Gap Analysis** — Missing skills
6. **Recommendations** — Development suggestions

---

*Back to [Tools Index](index.md) | See also: [!sk command](../commands/cmd-sk.md)*
