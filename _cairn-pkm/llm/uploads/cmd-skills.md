# Skill Evidence Tracking
*Type: Display/Write | Updated: 2025-01-01*

## Overview

Capture skill evidence from your work conversations. Builds a portfolio of demonstrated competencies over time.

**Shortcuts:** `!skills` to capture evidence, `!skills-report` to generate portfolio report

---

## Natural Evidence Capture

### From Conversation

After working through something:

```
LLM: That was a good debugging session. Want me to capture the skill evidence?
     
     I noticed you demonstrated:
     - Security (Level 4): Diagnosed and fixed certificate chain validation
     - Linux Administration (Level 3): Used openssl commands effectively
     - Troubleshooting (Level 4): Systematic root cause analysis
     
     Save this to your skill evidence log?
```

### What Gets Captured

From conversation, identify:

| Element | Example |
|---------|---------|
| Skill demonstrated | "Security", "Python Development" |
| Proficiency level | 1-5 based on complexity/independence |
| Evidence summary | "Diagnosed certificate chain validation issue" |
| Confidence | HIGH (clear outcome) or MEDIUM (partial) |

---

## Shortcut: !skills

Analyzes current conversation for skill evidence:

```
!skills
```

**Behavior:**
1. Scans conversation for technical work
2. Matches against skill patterns
3. Assigns proficiency levels
4. Shows draft for approval
5. Appends to skill-evidence.md

---

## Shortcut: !skills-report

Generates portfolio summary:

```
!skills-report
```

**Output:**
- Skills by level (highest first)
- Evidence count per skill
- Trend analysis
- Gap identification
- Recommendations

---

## Proficiency Levels

Default 5-level scale:

| Level | Name | Description |
|-------|------|-------------|
| 1 | Foundational | Learning with guidance |
| 2 | Developing | Can perform with some oversight |
| 3 | Proficient | Works independently |
| 4 | Advanced | Handles complex cases, guides others |
| 5 | Expert | Strategic impact, shapes practices |

---

## Level Determination

From conversation patterns:

| Pattern | Level |
|---------|-------|
| "basic", "learning", "assisted" | 2 |
| "independently", "resolved", "managed" | 3 |
| "architected", "designed", "led", "optimized" | 4 |
| "transformed", "established", "innovated" | 5 |

**Instance accumulation:**
- 2-4 instances → Level 2
- 5-9 instances → Level 3
- 10-19 instances → Level 4
- 20+ instances → Level 5

---

## Evidence Entry Format

```markdown
### 2025-01-01 - SSL Certificate Resolution
**Source:** Work session with Claude

**Context:** Resolved certificate chain validation issue

**Evidence Summary:** Independent troubleshooting of complex SSL issue

**Skills Identified:**
- Security Level 4: Diagnosed certificate chain validation in production
- Linux Administration Level 3: Used openssl to verify certificate chain
- Troubleshooting Level 4: Systematic elimination identified root cause

**Total Evidence:** 3 entries
**Confidence Distribution:** HIGH: 2, MEDIUM: 1

---
```

---

## File Structure

```
_local/data/skills/
├── skill-evidence.md           # Your accumulated evidence
├── skills-registry.yaml        # Optional: custom skill definitions
└── archive/
    └── skill-portfolio-report-YYYY-QN.md
```

---

## Custom Skill Registry

Override the default skill patterns by creating `_local/data/skills/skills-registry.yaml`:

```yaml
framework_name: "My Skills Framework"

levels:
  1: { name: "Beginner", description: "Learning" }
  2: { name: "Intermediate", description: "Applying" }
  3: { name: "Advanced", description: "Independent" }
  4: { name: "Expert", description: "Mentoring" }
  5: { name: "Master", description: "Innovating" }

categories:
  technical:
    name: "Technical Skills"
    skills:
      - id: python
        name: "Python Programming"
        keywords: ["python", "django", "flask"]
```

---

## Built-in Skill Detection

When no custom registry exists, detects:

**Technical:** python, javascript, cloud, docker, sql, git, api, linux, security, devops

**Patterns:**
- "configured [technology]" → Configuration, Level 3
- "debugged [issue]" → Troubleshooting, Level 3
- "designed [system]" → Architecture, Level 4
- "automated [process]" → Automation, Level 4

---

## Portfolio Report Format

```markdown
# Skill Portfolio Report
**Generated:** 2025-01-01
**Period:** 2024-07-01 to 2025-01-01

## Summary
3 Level 4 skills, 5 Level 3 skills demonstrated across 28 evidence entries.

## Skills by Level

### Level 4 (Advanced)
- **Security:** 12 instances
  Key: Certificate management, access control, audit compliance
  
- **Architecture:** 8 instances
  Key: System design, integration patterns
```

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| No technical content | "I didn't find skill-relevant activities. What technical work did you do?" |
| No evidence file | Creates new file with header |
| Parse error | "Skills registry has an issue. Using defaults." |
| Save fails | Fall back to download |
