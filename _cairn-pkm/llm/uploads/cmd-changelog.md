# Change Documentation
*Type: Read/Write | Updated: 2025-01-01*

## Overview

Document system changes with structured YAML entries. Captures what changed, why, and how to reverse it.

**Shortcut:** `!changelog` to document a change, `!changelog-r` to review history

---

## Natural Documentation

### From Conversation

After working through a technical issue:

```
User: OK, that SSL fix is done

LLM: Want me to document that change? I can capture:
     - What was changed (certificate configuration)
     - Why (cert was expiring, causing outages)
     - How to reverse it if needed
     
User: Yes

LLM: [Generates changelog entry]

     Here's the change record:
     
     Change ID: CHG-20250101-143022
     Title: Fixed SSL certificate chain validation
     Systems: webserver-prod
     Rationale: Certificate expiring caused service interruptions
     
     Want me to add or change anything before saving?
```

---

## What Gets Captured

From conversation, extract:

| Element | Source |
|---------|--------|
| Title | Summary of what was done |
| Systems affected | Hostnames, services, applications mentioned |
| Technologies | Languages, tools, protocols used |
| Problem | What was broken, error messages |
| Solution | Steps taken, commands run |
| Rationale | Why this change was necessary (RC) |
| AI contribution | What the assistant did |
| Rollback steps | Inferred reverse operations |

---

## Shortcut: !changelog

Immediately analyzes conversation and generates entry:

```
!changelog
```

**Behavior:**
1. Scans conversation for technical work
2. Extracts change details
3. Shows draft entry
4. Allows editing before save

---

## Entry Format

```yaml
change_id: CHG-{YYYYMMDD}-{HHMMSS}
timestamp: {ISO timestamp}
implemented_by: {from prefs or asked}
requested_by: {from prefs or asked}

title: "{concise summary}"
rationale: "{why this change was necessary}"

systems_affected:
  - {system}
technologies:
  - {tech}
problem_category: {security|performance|bug|feature|configuration}

change_classification:
  impact: {low|medium|high|critical}
  risk: {low|medium|high}
  reversibility: {instant|hours|days|permanent}
  scope: {single-system|area|organization-wide}

description: |
  {context}
  
problem: |
  {what was broken}
  
solution: |
  {what was done}

files_modified:
  - {file}

rollback:
  complexity: {estimated}
  estimated_time: "{time}"
  steps:
    - action: "{step}"
      validation: "{how to verify}"

validation:
  tested: {true/false}
  test_environment: {env}
  test_results: "{results}"

ai_contribution:
  role: {advisor|executor|validator|researcher}
  time_saved_estimate: {minutes}
  specific_contributions:
    - "{what AI did}"

track_context: "{project if applicable}"
created: {timestamp}
```

---

## Shortcut: !changelog-r

Review recent changes:

```
!changelog-r          # Last 30 days
!changelog-r 90       # Last 90 days
!changelog-r 2025-01  # Specific month
```

Shows:
- Date and title
- Systems affected
- Rationale
- Problem summary

---

## AI Role Selection

| Role | When to Use |
|------|-------------|
| advisor | AI suggested approach, human implemented |
| executor | AI wrote code/config that was used directly |
| validator | AI verified or tested the solution |
| researcher | AI found documentation or compared options |

---

## Rationale (RC Principle)

Always capture *why*, not just *what*:

```
Good: "Certificate expiring caused intermittent service outages"
Good: "Security audit flagged weak cipher configuration"
Bad:  (no rationale)
```

If not explicit in conversation, ask:
```
LLM: Why was this change necessary? (helps future reference)
```

---

## Storage

**Location:** `{VAULT_PATH}/_local/data/changelog/`

**Filename:** `{YYYY-MM-DD}-{HHMMSS}-{slug}.yaml`

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| Not enough context | "I need more details. What system was changed and what was the problem?" |
| Ambiguous systems | "Which system was actually changed — webserver or database?" |
| No rationale | "Why was this change necessary?" |
| Save fails | Fall back to download, show content |
