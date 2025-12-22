# !changelog - Change Tracking
*Type: Read/Write | Updated: 2025-12-21*

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
help                   - Show available commands
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

---

## AI Role Selection Guide

Choose the role that best describes the AI's primary contribution:

### advisor

**Select when:** AI provided guidance, suggestions, or explained concepts but human implemented

**Examples:**
- "You suggested using X approach instead of Y"
- "You explained how the template system works"
- "You recommended checking the logs for errors"
- "You pointed out a potential security issue"

**Contributions to log:**
- "Suggested nginx reverse proxy approach"
- "Explained OAuth flow and token refresh"
- "Recommended rate limiting strategy"

### executor

**Select when:** AI wrote working code, configuration, or scripts that were used directly

**Examples:**
- "You generated the Python script and it worked"
- "You created the nginx config file"
- "You wrote the SQL query that solved the problem"
- "You produced the shell script for automation"

**Contributions to log:**
- "Generated complete SSL renewal script"
- "Created nginx.conf with all required directives"
- "Wrote data migration SQL with validation"

### validator

**Select when:** AI verified, tested, or confirmed a solution would work

**Examples:**
- "You checked my config and found the error"
- "You validated the SQL query syntax"
- "You confirmed the approach was correct"
- "You reviewed the code and spotted the bug"

**Contributions to log:**
- "Verified SSL certificate chain validity"
- "Validated regex pattern against test cases"
- "Confirmed nginx config syntax correct"

### researcher

**Select when:** AI searched documentation, compared options, or provided reference material

**Examples:**
- "You found the API documentation"
- "You compared different database engines"
- "You looked up the correct syntax"
- "You researched alternative approaches"

**Contributions to log:**
- "Found official AWS documentation on IAM policies"
- "Compared nginx vs Apache for use case"
- "Researched Python asyncio best practices"

**Multiple roles:** If AI performed multiple roles, select the primary one and note others in specific_contributions.

---

## Time Saved Estimation Guide

Estimate time saved by AI assistance compared to doing the work manually.

### Estimation Framework

**Simple fix (15-30 min):**
- Syntax corrections
- Single config change with known solution
- Lookup of documented command/setting
- **Basis:** "Quick syntax fix from documentation"

**Research + solution (30-60 min):**
- Finding correct documentation
- Comparing 2-3 options
- Generating standard boilerplate code
- **Basis:** "Documentation search and configuration generation"

**Complex debugging (60-120 min):**
- Multi-step troubleshooting
- Identifying root cause in complex system
- Testing multiple solutions
- **Basis:** "Systematic debugging saved trial-and-error"

**Code generation (30-90 min):**
- Writing tested, working code
- Complete script with error handling
- Configuration from scratch
- **Basis:** "Generated working code with validation"

### Example Estimates with Reasoning

| Task | Estimate | Basis |
|------|----------|-------|
| Generated SSL renewal script | 60 min | "Script generation, cron setup, testing" |
| Found correct API endpoint in docs | 20 min | "Documentation search and verification" |
| Debugged nginx routing issue | 90 min | "Systematic testing of config combinations" |
| Fixed Python syntax error | 15 min | "Immediate syntax correction" |
| Compared 3 database options | 45 min | "Research, comparison, recommendation" |
| Created complete YAML config | 40 min | "Template creation with validation" |

**Conservative estimates:** When uncertain, estimate lower. It's better to underestimate than overestimate.

**Compound tasks:** Sum the components. Script generation (40 min) + documentation (20 min) + testing (10 min) = 70 min total.

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
