# !change — Change Tracking

**Type:** Write | **Version:** 2.1

Document production changes with structured YAML entries.

---

## Quick Reference

| Command | What Happens | Permission |
|---------|--------------|------------|
| `!change` | Extract change from conversation | Confirmation required |

---

## Workflow

1. Complete a production change
2. Type `!change`
3. Review draft with extracted details
4. Provide `requested_by` (required)
5. Edit fields as needed
6. Type `save` to create file

---

## What It Extracts

From conversation:
- Systems affected
- Technologies used
- Problem description
- Solution implemented
- Files touched
- AI contribution
- Related tasks

---

## Interactive Commands

```
save                    # Create file
requested_by [name]     # Set requester (required)
edit [field]            # Modify any field
cancel                  # Discard entry
```

---

## Output Location

`_local/data/changelog/YYYY/MM/CHG-YYYYMMDD-HHMMSS.yaml`

---

## YAML Schema

```yaml
change_id: CHG-YYYYMMDD-HHMMSS
timestamp: YYYY-MM-DDTHH:MM:SS
implemented_by: WebOps
requested_by: ""  # REQUIRED

title: "Concise one-line summary"
systems_affected:
  - hostname.example.com
technologies:
  - SSL
  - Nginx
problem_category: security | performance | bug | feature | configuration

change_classification:
  impact: low | medium | high | critical
  risk: low | medium | high
  reversibility: instant | hours | days | permanent
  scope: single-system | area | organization-wide

description: |
  Brief context

problem: |
  What was broken

solution: |
  What was done

files_modified:
  - /path/to/file

rollback:
  complexity: low | medium | high
  estimated_time: "15 minutes"
  steps:
    - action: "Reverse step"
      validation: "How to verify"

validation:
  tested: true
  test_environment: staging
  test_results: "All passed"

ai_contribution:
  role: advisor | executor | validator | researcher
  time_saved_estimate: 45
  time_saved_basis: "Research and debugging"
  specific_contributions:
    - "Suggested configuration approach"

related_changes: []
track_context: "p042-ssl-fix"
session_context: "Brief description"
```

---

## Classification Guide

### Problem Category

| Category | Use for |
|----------|---------|
| security | SSL, auth, vulnerabilities, patches |
| performance | Speed, optimization, resources |
| bug | Something broken |
| feature | New capability |
| configuration | Settings, deployment, setup |

### Impact

| Level | Criteria |
|-------|----------|
| low | Single minor component |
| medium | Multiple components, some visibility |
| high | Critical systems, significant impact |
| critical | Production down, emergency |

### Risk

| Level | Criteria |
|-------|----------|
| low | Simple, well-tested, easy to reverse |
| medium | Complex but tested, some unknowns |
| high | Risky, limited testing, hard to reverse |

### Reversibility

| Level | Criteria |
|-------|----------|
| instant | Config toggle, simple revert |
| hours | Restore backup, re-deploy |
| days | Complex data migration reversal |
| permanent | Deleted data, irreversible |

### AI Role

| Role | Description |
|------|-------------|
| advisor | Suggested approach |
| executor | Wrote working code/config |
| validator | Verified solution |
| researcher | Found documentation |

---

## Best Practices

**DO document:**
- Production changes
- Configuration changes
- Security updates
- Bug fixes
- Emergency repairs

**DON'T document:**
- Development work (pre-production)
- Planning sessions
- Meetings without changes

**Good titles:**
- ✅ "Fixed Redis cache configuration causing slow homepage"
- ❌ "Redis issue"

---

## Searching Changes

```bash
# By technology
grep -r "ssl" _local/data/changelog/

# By system
grep -l "example.com" _local/data/changelog/2025/*/*.yaml

# By impact
grep -l "impact: high" _local/data/changelog/2025/*/*.yaml
```

---

*Back to [Commands Index](index.md) | See also: [Changelog Tool](../tools/changelog.md)*
