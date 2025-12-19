# !change - Change Tracking
*Type: Read/Write | Version: 3.0 | Updated: 2025-12-18*

## Quick Reference

| Action | What Happens | Output Mode |
|--------|--------------|-------------|
| `!change` | Extract change from conversation, create YAML entry | Per user-prefs.yaml |

**Workflow:** Type `!change` → Review draft → Edit/provide missing fields → `save` → Output per prefs

---

## Requirements

**Assistant Capabilities:**
- Conversation context analysis
- YAML generation
- File system access (if file_operations = write or confirm)
- Timestamp generation

**User Configuration:**
- `_local/user-prefs.yaml` — file_operations setting, timezone

**Vault Structure:**
- `_local/data/changelog/` — Change log storage with YYYY/MM subdirectories

---

## Initialization

```
READ: {VAULT_PATH}/_local/user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - timezone (default: "America/Los_Angeles")
  - default_assignee (default: "")
```

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
SET implemented_by from default_assignee
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

### Phase 6: Output
```
CONSTRUCT: filepath = {VAULT_PATH}/_local/data/changelog/{YYYY}/{MM}/{change_id}.yaml
CONSTRUCT: content = {final_yaml}

CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

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
implemented_by: {default_assignee from prefs}
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

## Output Examples

### Draft Phase

```
🕐 Current Date/Time: December 18, 2025 at 14:30 PST
Extracting change information from conversation...

📝 CHANGE ENTRY DRAFT
═══════════════════════════════════════
change_id: CHG-20251218-143000
timestamp: 2025-12-18T14:30:00-08:00
implemented_by: WebOps
requested_by: ""

title: "Fixed SSL certificate chain validation on staging"
systems_affected:
  - staging.example.org
technologies:
  - SSL/TLS
  - nginx
problem_category: security
...
═══════════════════════════════════════

Missing required field:
• requested_by: Who requested this change?

Commands:
• Type 'edit [field]' to modify any field
• Type 'requested_by [name]' to set requester
• Type 'save' when ready to create file
• Type 'cancel' to discard
```

### Completion (display mode)

```
📄 FILE CONTENT
═══════════════════════════════════════
Filename: CHG-20251218-143000.yaml
Path: {VAULT_PATH}/_local/data/changelog/2025/12/

{complete YAML content}

═══════════════════════════════════════
Copy this content and save to the path above.

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

### Completion (write mode)

```
✓ Created {VAULT_PATH}/_local/data/changelog/2025/12/CHG-20251218-143000.yaml

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

### Completion (confirm mode)

```
📄 PROPOSED FILE
═══════════════════════════════════════
Filename: CHG-20251218-143000.yaml
Path: {VAULT_PATH}/_local/data/changelog/2025/12/

{complete YAML content}

═══════════════════════════════════════
Write this file? (yes/no)
```

Then on confirmation:

```
✓ Created {VAULT_PATH}/_local/data/changelog/2025/12/CHG-20251218-143000.yaml

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Conversation too short | "⚠️ Not enough information. Please describe: system, problem, solution" |
| Ambiguous systems | "Which system was actually changed?" |
| Missing required on save | "❌ Missing required field: requested_by" |
| user-prefs.yaml missing | Use defaults: display mode, local target |
| Write fails (write/confirm mode) | Report error, fall back to display mode |

---

## File Paths

| Purpose | Path |
|---------|------|
| User prefs | `{VAULT_PATH}/_local/user-prefs.yaml` |
| Base | `{VAULT_PATH}/_local/data/changelog/` |
| Output | `/YYYY/MM/CHG-YYYYMMDD-HHMMSS.yaml` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.1 | 2025-12-16 | Previous version (confirmation required) |
| 3.0 | 2025-12-18 | Added user-prefs support, configurable output mode |
