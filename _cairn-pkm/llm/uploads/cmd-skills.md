# !skills - Skill Evidence Tracker
*Type: Display/Write | Version: 0.9.0 | Updated: 2025-12-21*

## Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `!skills` | Analyze current conversation | After technical discussions |
| `!skills-report` | Generate portfolio snapshot | Monthly/quarterly, before reviews |

**Workflow:** Technical work → `!skills` → Copy output → Paste to skill-evidence.md

**Location:** `{VAULT_PATH}/_local/data/skills/`

---

## Framework-Agnostic Design

This command works with **any skill taxonomy**:
- Technical competency matrices
- Role-based skill ladders
- Certification frameworks
- Custom organizational competencies
- Personal learning goals

Configure your own skill definitions or use the built-in defaults.

---

## Configuration

### Default Proficiency Levels

If no custom registry exists, uses this 5-level scale:

| Level | Name | Description |
|-------|------|-------------|
| 1 | Foundational | Basic awareness, learning with guidance |
| 2 | Developing | Can perform with some oversight |
| 3 | Proficient | Works independently, handles typical cases |
| 4 | Advanced | Handles complex cases, guides others |
| 5 | Expert | Strategic impact, shapes practices |

### Registry Discovery

The command searches for a skills registry in this order:

1. `_local/data/skills/skills-registry.yaml` (user custom)
2. Built-in defaults (no file needed)

If no registry file exists, built-in detection patterns and the default 5-level scale are used.

### Custom Skill Registry (Optional)

Create `_local/data/skills/skills-registry.yaml` to define your own framework.

See `_cairn-pkm/templates/examples/skills-registry-professional.yaml` for a complete example.

```yaml
# Example: Custom skill registry
framework_name: "My Skills Framework"
framework_version: "1.0"

# Define your proficiency levels
levels:
  1:
    name: "Beginner"
    description: "Learning fundamentals"
  2:
    name: "Intermediate"
    description: "Applies knowledge with guidance"
  3:
    name: "Advanced"
    description: "Works independently"
  4:
    name: "Expert"
    description: "Mentors others, handles edge cases"
  5:
    name: "Master"
    description: "Innovates, sets direction"

# Define skill categories and skills
categories:
  technical:
    name: "Technical Skills"
    skills:
      - id: python
        name: "Python Programming"
        keywords: ["python", "django", "flask", "pandas"]
      - id: cloud
        name: "Cloud Infrastructure"
        keywords: ["aws", "azure", "gcp", "terraform"]
  
  soft:
    name: "Soft Skills"
    skills:
      - id: communication
        name: "Communication"
        keywords: ["presented", "documented", "explained", "trained"]
      - id: leadership
        name: "Leadership"
        keywords: ["led", "mentored", "coordinated", "delegated"]

# Level determination rules (optional overrides)
level_rules:
  instance_thresholds:
    level_2: 2    # 2-4 instances
    level_3: 5    # 5-9 instances
    level_4: 10   # 10-19 instances
    level_5: 20   # 20+ instances
  
  # Achievements that auto-qualify for specific levels
  achievements:
    level_4:
      - "99.9% uptime"
      - "cost savings over $50k"
      - "automation implementation"
    level_5:
      - "organization-wide adoption"
      - "industry recognition"
      - "patent or publication"
```

---

## !skills — Analyze Session

### Process

1. Scan conversation for technical activities
2. Match against skill patterns (registry or built-in)
3. Apply level determination rules
4. Generate evidence summary

### Output Format

```markdown
### YYYY-MM-DD - [Session Topic]
**Source:** [Conversation context or empty]

**Context:** [What was accomplished or empty]

**Evidence Summary:** [One-line summary or empty]

**Skills Identified:**
- SKILL Level N: [single-line evidence]
- SKILL Level N: [single-line evidence]

**Total Evidence:** N entries
**Confidence Distribution:** HIGH: N, MEDIUM: N
```

### Level Determination (Defaults)

| Instance Count | Level |
|----------------|-------|
| 2-4 validated | Level 2 |
| 5-9 validated | Level 3 |
| 10-19 validated OR special achievement | Level 4 |
| 20+ validated OR transformational impact | Level 5 |

**Special Achievements (Auto Level 4):** High uptime (99.9%+), automation implementation, significant cost savings ($50k+)

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
**Framework:** [Framework name or "Default"]
**Period Covered:** [First entry] to [Last entry]
**Total Entries Analyzed:** N

## Executive Summary
[2-3 sentence overview]

## Current Skill Levels

### Level 5 Skills (Expert/Strategic)
- **SKILL - Full Name:** N total instances
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
- SKILL: [Why valuable based on context]

## Progression Recommendations
[Strategic suggestions based on current trajectory]
```

---

## Built-in Skill Detection

When no custom registry exists, uses these patterns:

### Technical Skills (Auto-Detected)

| Keyword Pattern | Skill Category |
|-----------------|----------------|
| python, django, flask | Python Development |
| javascript, react, node | JavaScript/Frontend |
| aws, azure, gcp, cloud | Cloud Infrastructure |
| docker, kubernetes, k8s | Containerization |
| sql, postgres, mysql | Database |
| git, github, version control | Version Control |
| api, rest, graphql | API Development |
| linux, bash, shell | Systems Administration |
| security, ssl, auth | Security |
| ci/cd, jenkins, github actions | DevOps |

### Action + Outcome Patterns

| Pattern | Detected As |
|---------|-------------|
| "configured [technology]" | Configuration skill, Level 3 |
| "debugged [issue]" | Troubleshooting, Level 3 |
| "designed [system]" | Architecture, Level 4 |
| "mentored [person/team]" | Leadership, Level 4 |
| "automated [process]" | Automation, Level 4 |
| "maintained 99.9% uptime" | Operations, Level 4 |

### Complexity Indicators

| Level | Keywords |
|-------|----------|
| Level 2 | basic, simple, routine, assisted, learning |
| Level 3 | independently, resolved, managed, implemented |
| Level 4 | architected, designed, led, strategic, optimized |
| Level 5 | transformed, established, governed, innovated |

---

## Confidence Scoring

| Confidence | Criteria |
|------------|----------|
| HIGH | Clear action + measurable outcome, quantifiable metrics, independent production work |
| MEDIUM | Standard task completion, some collaboration, staging environment work |
| LOW | Vague descriptions, heavy assistance required, no clear outcome |

---

## File Structure

```
_local/data/skills/
├── skill-evidence.md        # Accumulated evidence (append-only)
├── skills-registry.yaml     # Optional custom framework
└── archive/                  # Historical reports
```

### skill-evidence.md Format

```markdown
# Skill Evidence Log

## 2025-12

### 2025-12-21 - SSL Certificate Resolution
**Source:** Work session with Claude

**Context:** Resolved certificate chain validation issue for production site

**Evidence Summary:** Independent troubleshooting of complex SSL issue

**Skills Identified:**
- Security Level 4: Diagnosed and fixed certificate chain validation in production
- Linux Administration Level 3: Used openssl commands to verify certificate chain
- Troubleshooting Level 4: Identified root cause through systematic elimination

**Total Evidence:** 3 entries
**Confidence Distribution:** HIGH: 2, MEDIUM: 1

---

[Previous entries...]
```

---

## Operational Rules

1. Execute immediately (no permission gates)
2. Use single-line evidence format
3. Always include confidence distribution
4. Read actual files (don't work from memory)
5. skill-evidence.md is append-only (user pastes after review)
6. If custom registry exists, use its definitions
7. If no registry, use built-in defaults

**Complete:** Per `cmd-shared-patterns.md`

---

## Example Custom Frameworks

### Software Engineering Ladder

```yaml
framework_name: "Engineering Levels"
levels:
  1: { name: "Junior", description: "Learning with guidance" }
  2: { name: "Mid-Level", description: "Independent contributor" }
  3: { name: "Senior", description: "Leads projects, mentors" }
  4: { name: "Staff", description: "Cross-team impact" }
  5: { name: "Principal", description: "Org-wide influence" }
```

### Certification Tracking

```yaml
framework_name: "Certification Progress"
levels:
  1: { name: "Studying", description: "Currently learning material" }
  2: { name: "Practicing", description: "Hands-on experience" }
  3: { name: "Ready", description: "Prepared for exam" }
  4: { name: "Certified", description: "Passed certification" }
  5: { name: "Expert", description: "Teaching/mentoring others" }
```

### Personal Learning Goals

```yaml
framework_name: "Learning Journey"
levels:
  1: { name: "Curious", description: "Exploring the topic" }
  2: { name: "Learning", description: "Active study" }
  3: { name: "Applying", description: "Using in projects" }
  4: { name: "Comfortable", description: "Reliable skill" }
  5: { name: "Teaching", description: "Can explain to others" }
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| No technical content | "No skill-relevant activities detected in this conversation" |
| Registry parse error | "Could not parse skills-registry.yaml: {error}. Using defaults." |
| skill-evidence.md not found | "skill-evidence.md not found. Output will be displayed for manual creation." |
| Unknown skill pattern | Capture anyway with generic category, note for user review |

Common errors: See `cmd-shared-patterns.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.9.0 | 2025-12-21 | Removed SFIA dependency, added framework-agnostic design with custom registry support |
| 0.8.1 | 2025-12-20 | Fixed output formatting - added line breaks between Source/Context/Evidence Summary fields |
| 0.8.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.7.1 | 2025-12-19 | Renamed from !sk to !skills |
| 0.7.0 | 2025-12-19 | Reset to pre-release versioning |
