# !changelog - Change Tracking
*Type: Read/Write | Version: 0.11.0 | Updated: 2025-12-21*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!changelog` | Extract change from conversation, create YAML entry |
| `!changelog-r` | Display last 30 days of changes |
| `!changelog-r [days]` | Display last N days |
| `!changelog-r [YYYY-MM]` | Display specific month |
| `!changelog-r [start] [end]` | Display date range |

**Workflow:**
- **Create:** `!changelog` → Review draft → Edit/provide fields as needed → `save`
- **Review:** `!changelog-r` → View narrative history

**Location:** `{VAULT_PATH}/_local/data/changelog/`

---

## Initialization

Per `cmd-shared-patterns.md`

---

## !changelog - Create Change Entry

### Execution

1. Display current date/time
2. Analyze conversation for change details
3. Generate draft YAML with change_id `CHG-{YYYYMMDD}-{HHMMSS}`
4. Interactive edit loop until `save`
5. Output per file_operations setting

### Conversation Analysis

```
SCAN FOR:
- systems_mentioned: Hostnames, domains, servers, applications
- technologies_used: Languages, frameworks, tools, protocols
- problem_described: Error messages, symptoms, what was broken
- solution_implemented: Steps taken, commands run, fixes applied
- files_touched: Config files, code files, certificates
- ai_contributions: What assistant suggested, generated, validated
- rationale: Why this change was necessary (RC principle)

EXTRACT:
- Title: Concise one-line summary (action-oriented)
- Problem: Problem description with context
- Solution: Step-by-step solution from conversation
- Rationale: Why this change was made (business/technical driver)
- AI role: advisor/executor/validator/researcher
- Time saved: Estimate based on complexity
- Rollback steps: Inferred reverse operations
```

### Interactive Commands

```
save                   - Finalize and output
requested_by [name]    - Set requester
rationale [text]       - Set/update rationale (RC principle)
edit [field]           - Modify any field
cancel                 - Discard entry
```

**Complete:** Per `cmd-shared-patterns.md`

---

## !changelog-r - Review History

### Date Parsing

| Input | Result |
|-------|--------|
| `!changelog-r` | Last 30 days |
| `!changelog-r 90` | Last 90 days |
| `!changelog-r 2025-12` | December 2025 |
| `!changelog-r 2025-11-01 2025-12-31` | Date range |

### Output Format

For each change (newest first):
- Date and title
- Filename
- Rationale (if present)
- Systems affected
- Implemented by
- Problem summary (first 150 chars)

---

## YAML Template

```yaml
change_id: {auto-generated}
timestamp: {auto-generated}
implemented_by: {default_assignee from prefs}
requested_by: {default_assignee from prefs}  # Falls back to "" if default_assignee empty

title: "{extracted}"
rationale: "{why this change was necessary}"  # RC principle - captures the 'why'
systems_affected:
  - {extracted}
technologies:
  - {extracted}
problem_category: {inferred}

change_classification:
  impact: low | medium | high | critical
  risk: low | medium | high
  reversibility: instant | hours | days | permanent
  scope: single-system | area | organization-wide

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
  role: advisor | executor | validator | researcher
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

**Title:** Action verb + object ("Fixed X", "Updated Y"), under 80 chars

**Rationale (RC Principle):**
Extract the business or technical driver for the change:
- Look for: "because", "due to", "since", "needed to", "required for"
- If not explicit, infer from problem description
- Format: One sentence explaining *why* the change was necessary

| Conversation Context | Extracted Rationale |
|---------------------|---------------------|
| "SSL certs were expiring and causing outages" | "Certificates approaching expiration caused service interruptions" |
| "Users complained about slow page loads" | "Performance issues impacting user experience" |
| "Security audit flagged this configuration" | "Compliance requirement from security audit" |
| "We need this for the new feature" | "Prerequisite for upcoming feature deployment" |

**Problem Category:**

| Context | Category |
|---------|----------|
| SSL/certificates/auth | security |
| Slow/timeout/optimization | performance |
| Error/broken/not working | bug |
| New capability/enhancement | feature |
| Config/deployment/setup | configuration |

**Impact:** low (minor component) → medium (some user visibility) → high (critical systems) → critical (production down)

**Risk:** low (simple, tested, reversible) → medium (complex but tested) → high (risky, limited testing)

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
| Conversation too short | "Not enough information. Describe: system, problem, solution" |
| Ambiguous systems | "Which system was actually changed?" |
| No rationale found | Prompt: "Why was this change necessary?" |
| No changelog directory (!changelog-r) | "No changelog directory found at {path}" |
| No changes in range (!changelog-r) | "No changes found for period {start} to {end}" |
| Invalid date format (!changelog-r) | "Invalid date format. Use: YYYY-MM-DD" |
| YAML parse error (!changelog-r) | Skip file, note: "Could not parse {filename}" |

Common errors: See `cmd-shared-patterns.md`

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.11.0 | 2025-12-21 | Added rationale field and extraction rules (RC principle) |
| 0.10.0 | 2025-12-20 | Use default_assignee as default for requested_by field |
| 0.9.0 | 2025-12-19 | Streamlined format, moved common patterns to shared |
| 0.8.0 | 2025-12-19 | Renamed from !change to !changelog |
| 0.7.0 | 2025-12-19 | Simplified to flat directory structure |
