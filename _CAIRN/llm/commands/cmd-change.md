# !change - Change Tracking
*Type: Write | Version: 2.1 | Updated: 2025-12-16*

## Quick Reference

| Action | What Happens | Permission |
|--------|--------------|------------|
| `!change` | Extract change from conversation, create YAML entry | Confirmation required |

**Workflow:** Type `!change` → Review draft → Edit/provide missing fields → `save` → File created

---

## Requirements

**Assistant Capabilities:**
- Conversation context analysis
- YAML generation
- File system write access
- Timestamp generation

**Vault Structure:**
- `System/tools/changelog/` — Change log storage with YYYY/MM subdirectories

---

## Execution

### Phase 1: DateTime
```
OUTPUT: "🕐 Current Date/Time: {Month DD, YYYY} at HH:MM {TIMEZONE}"
OUTPUT: "Extracting change information from conversation..."
```

### Phase 2: Analyze Conversation
```
SCAN FOR:
- systems_mentioned: Hostnames, domains, servers, applications
- technologies_used: Languages, frameworks, tools, protocols
- problem_described: Error messages, symptoms, what was broken
- solution_implemented: Steps taken, commands run, fixes applied
- files_touched: Config files, code files, certificates
- ai_contributions: What assistant suggested, generated, validated
- conversation_duration: Estimated work time
- related_tasks: Task/project references

EXTRACT:
- Title: Concise one-line summary (action-oriented)
- Problem: Problem description with context
- Solution: Step-by-step solution from conversation
- AI role: advisor/executor/validator/researcher
- Time saved: Estimate based on complexity
- Rollback steps: Inferred reverse operations
```

### Phase 3: Generate Draft
```
GENERATE: change_id = CHG-{YYYYMMDD}-{HHMMSS}
GENERATE: timestamp = {YYYY-MM-DDTHH:MM:SS{TIMEZONE}}

CREATE YAML DRAFT with all extracted fields
Mark requested_by as empty (REQUIRES USER INPUT)
```

### Phase 4: Prompt for Missing
```
OUTPUT: "📝 CHANGE ENTRY DRAFT"
OUTPUT: {formatted_yaml_preview}
OUTPUT: ""
OUTPUT: "Missing required field:"
OUTPUT: "• requested_by: Who requested this change?"
OUTPUT: ""
OUTPUT: "Commands:"
OUTPUT: "• Type 'edit [field]' to modify any field"
OUTPUT: "• Type 'requested_by [name]' to set requester"
OUTPUT: "• Type 'save' when ready to create file"
OUTPUT: "• Type 'cancel' to discard"
```

### Phase 5: Interactive Edit
```
LOOP:
  WAIT FOR: user_input
  
  IF "save": validate required fields, GOTO Phase 6
  IF "requested_by [name]": UPDATE field, CONTINUE
  IF "edit [field]": prompt for new value, UPDATE, CONTINUE
  IF "cancel": OUTPUT "Change entry discarded", STOP
```

### Phase 6: Create File
```
CONSTRUCT: filepath = {VAULT_PATH}/System/tools/changelog/{YYYY}/{MM}/{change_id}.yaml
CREATE: directory if not exists
WRITE: final_yaml to filepath

OUTPUT: "✓ Change entry created"
OUTPUT: "File: {filepath}"
OUTPUT: "ID: {change_id}"
```

### Phase 7: Completion
```
OUTPUT:
✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════

STOP
```

---

## YAML Template

```yaml
change_id: {auto-generated}
timestamp: {auto-generated}
implemented_by: {default_user}
requested_by: ""  # REQUIRES USER INPUT

title: "{extracted}"
systems_affected:
  - {extracted}
technologies:
  - {extracted}
problem_category: {inferred}

change_classification:
  impact: {estimated}
  risk: {estimated}
  reversibility: {estimated}
  scope: {estimated}

description: |
  {extracted_context}
  
problem: |
  {extracted_problem}
  
solution: |
  {extracted_steps}

files_modified:
  - {extracted}

rollback:
  complexity: {estimated}
  estimated_time: "{estimated}"
  steps:
    - action: "{reverse_step}"
      validation: "{validation}"

validation:
  tested: {true/false}
  test_environment: {extracted}
  test_results: "{extracted}"

ai_contribution:
  role: {determined}
  time_saved_estimate: {minutes}
  time_saved_basis: "{reasoning}"
  specific_contributions:
    - "{contribution}"

related_changes: []
track_context: "{extracted}"
session_context: "{brief}"

created: {timestamp}
updated: {timestamp}
```

---

## Extraction Rules

**Title Generation:**
- Action verb + object: "Fixed X", "Updated Y", "Configured Z"
- Under 80 characters
- Specific, not generic

**Problem Category:**
| Context | Category |
|---------|----------|
| SSL/certificates/auth | security |
| Slow/timeout/optimization | performance |
| Error/broken/not working | bug |
| New capability/enhancement | feature |
| Config/deployment/setup | configuration |

**Impact Assessment:**
| Level | Criteria |
|-------|----------|
| low | Single minor component, no user impact |
| medium | Multiple components or some user visibility |
| high | Critical systems, significant user impact |
| critical | Production down, emergency fix |

**Risk Assessment:**
| Level | Criteria |
|-------|----------|
| low | Simple change, well-tested, easy to reverse |
| medium | Complex but tested, some unknowns |
| high | Risky change, limited testing, hard to reverse |

**Reversibility:**
| Level | Criteria |
|-------|----------|
| instant | Config toggle, feature flag, simple revert |
| hours | Restore from backup, re-deploy previous |
| days | Complex data migration reversal |
| permanent | Deleted data, irreversible actions |

**Scope:**
| Level | Criteria |
|-------|----------|
| single-system | One hostname/app affected |
| area | Multiple systems in same function |
| organization-wide | Affects everyone/everything |

**AI Role:**
| Role | Description |
|------|-------------|
| advisor | Suggested approach, explained concept |
| executor | Wrote working code/config |
| validator | Verified solution would work |
| researcher | Found docs, compared options |

**Time Saved Estimate:**
- Simple fix: 15-30 min
- Research + solution: 30-60 min
- Complex debugging: 60-120 min
- Code generation: 30-90 min

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation too short | "⚠️ Not enough information. Please describe: system, problem, solution" |
| Ambiguous systems | "Which system was actually changed?" |
| Missing required on save | "❌ Missing required field: requested_by" |

---

## File Paths

| Purpose | Path |
|---------|------|
| Base | `{VAULT_PATH}/System/tools/changelog/` |
| Output | `/YYYY/MM/CHG-YYYYMMDD-HHMMSS.yaml` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-11-05 | Initial version |
| 2.0 | 2025-12-15 | LLM-agnostic refactor |
| 2.1 | 2025-12-16 | Standardized format |
