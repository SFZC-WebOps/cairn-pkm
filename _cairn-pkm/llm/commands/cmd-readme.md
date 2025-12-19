# !readme - Reference Research
*Type: Read + Web Search + Write | Version: 3.0 | Updated: 2025-12-18*

## Quick Reference

| Command | What Happens | Output Mode |
|---------|--------------|-------------|
| `!readme` | Analyze URL in current conversation | Per user-prefs.yaml |
| `!readme [url]` | Fetch and analyze specific URL | Per user-prefs.yaml |

**Workflow:** Paste URL → `!readme` → Get strategic analysis and score → Output per prefs

---

## Requirements

**Assistant Capabilities:**
- Web page fetching
- Web search (for context research)
- File system access (if file_operations = write or confirm)
- User context awareness (projects, interests)

**User Configuration:**
- `_local/user-prefs.yaml` — file_operations setting, references_folder

**Vault Structure:**
- References folder for saving analyzed content (per references_folder setting)

---

## Initialization

```
READ: {VAULT_PATH}/_local/user-prefs.yaml
EXTRACT:
  - file_operations (default: "display")
  - write_target (default: "local")
  - gdrive_vault_path (default: "")
  - timezone (default: "America/Los_Angeles")

HARDCODED DEFAULTS:
  - references_folder = "Objects/references"
```

---

## Execution

### Phase 1: Extract & Fetch
```
SCAN conversation for URL
IF no URL found: "No URL detected. Paste a link or use !readme [url]"
EXECUTE: web_fetch(url)
EXTRACT: title, author, date, main content
```

### Phase 2: Synthesize Content
```
PARSE: Key points, thesis, conclusions
IDENTIFY: Type (article|tool|documentation|video|research|tutorial)
SUMMARIZE: 3-5 sentence synopsis
```

### Phase 3: Deep Context Research
```
REQUIRED SEARCHES:
1. "[author name]" — Who is this person, credibility, affiliations
2. "[main topic] [current year]" — Is this current or outdated thinking
3. "[tool/concept] alternatives comparison" — What else exists

CONDITIONAL SEARCHES:
4. IF tool/product: "[product name] reviews problems" — Known issues
5. IF technical: "[concept] implementation examples" — Practical application
6. IF theory/research: "[concept] criticism" — Counterarguments

SYNTHESIZE:
- Author credibility score (established expert | emerging voice | unknown | questionable)
- Field currency (cutting edge | current | dated | obsolete)
- Competitive landscape (best in class | one of many | inferior options exist)
```

### Phase 4: Strategic Analysis
```
LOAD USER CONTEXT:
- Active projects/tracks from vault
- Known priorities and interests
- Current pain points and goals

EVALUATE AGAINST:
1. Direct project application — Does this solve a current problem?
2. Skill development — Does this build relevant capability?
3. Future planning — Does this inform upcoming decisions?
4. General enrichment — Interesting but no immediate use?

ASSESS STRATEGIC FIT:
- Timing: Right now | Next quarter | Someday | Never
- Effort to apply: Trivial | Moderate | Significant
- Risk if ignored: High | Medium | Low | None
```

### Phase 5: Usefulness Ranking
```
CALCULATE UTILITY SCORE (1-10):

+3 Solves active problem in current project
+2 Directly relevant to known priority area
+2 Author is established expert in relevant field
+1 Content is current (< 12 months old)
+1 Actionable (clear steps, not just theory)
+1 No significant alternatives that are better
+1 Low effort to apply

-1 Content is dated (> 2 years old)
-1 Author credibility unclear
-1 Already have equivalent knowledge/tool
-2 Tangential to actual work
-2 High effort, low certainty of payoff

SCORE INTERPRETATION:
8-10: 🔥 High Priority — Act on this soon
5-7:  ✅ Worth Filing — Reference for future
3-4:  🤔 Maybe — Skim and decide
1-2:  ❌ Skip — Not worth your time
```

### Phase 6: Output
```
GENERATE filename:
  score = UTILITY_SCORE
  slug = extract_slug(title, 4-5 words, lowercase, hyphenated, max 40 chars)
  filename = "{YYYY-MM-DD}-{score}of10-{slug}.md"

CONSTRUCT: filepath = {VAULT_PATH}/Objects/references/{filename}
CONSTRUCT: content = {report content per output format below}

CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

---

## Output Format

```markdown
# {descriptive_title}
**Source:** [url]
**Type:** [article|tool|documentation|video|research|tutorial|other]
**Author:** [name] | **Credibility:** [established expert|emerging voice|unknown|questionable]
**Published:** [date] | **Currency:** [cutting edge|current|dated|obsolete]

### Synopsis
[What this is, key points, 3-5 sentences]

### Context Research
**Author Background:** [Who they are, relevant experience, other notable work]
**Field Position:** [How this ranks against alternatives, competitive landscape]
**Critical View:** [Known criticisms, limitations, counterarguments]

### Strategic Fit

| Factor | Assessment |
|--------|------------|
| **Relevant Projects** | [track identifier or "None directly"] |
| **Area Alignment** | [area + why] |
| **Timing** | [Right now | Next quarter | Someday] |
| **Effort to Apply** | [Trivial | Moderate | Significant] |
| **Risk if Ignored** | [High | Medium | Low | None] |

### Utility Score: [X]/10 [emoji]
**Scoring Breakdown:**
[+/- factors that led to score]

### Recommendation
**Action:** [Act Now | File for Reference | Add to Track | Skim & Decide | Skip]
**If Filing, Tags:** #tag #tag #tag
**Next Step:** [Specific action if score > 5]
```

---

## Output Examples

### Completion (display mode)

```
📄 FILE CONTENT
═══════════════════════════════════════
Filename: 2025-12-18-7of10-api-design-patterns.md
Path: {VAULT_PATH}/Objects/references/

{complete report content}

═══════════════════════════════════════
Copy this content and save to the path above.

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

### Completion (write mode)

```
✓ Created {VAULT_PATH}/Objects/references/2025-12-18-7of10-api-design-patterns.md

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

### Completion (confirm mode)

```
📄 PROPOSED FILE
═══════════════════════════════════════
Filename: 2025-12-18-7of10-api-design-patterns.md
Path: {VAULT_PATH}/Objects/references/

{complete report content}

═══════════════════════════════════════
Write this file? (yes/no)
```

Then on confirmation:

```
✓ Created {VAULT_PATH}/Objects/references/2025-12-18-7of10-api-design-patterns.md

✓ Task complete
═══════════════════════════════════════
🤖 Waiting for next instruction
═══════════════════════════════════════
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | "⚠️ Could not fetch [url] — check link or try again" |
| Paywall/login required | "⚠️ Content behind paywall. Working with available preview..." Score -1 |
| No author/date found | "Author: Unknown | Published: Unknown" — Credibility = unknown |
| Search results sparse | "Limited context available — scoring may be less reliable" |
| user-prefs.yaml missing | Use defaults: display mode, local target |
| Write fails (write/confirm mode) | Report error, fall back to display mode |

---

## File Paths

| Purpose | Path |
|---------|------|
| User prefs | `{VAULT_PATH}/_local/user-prefs.yaml` |
| Output | `{VAULT_PATH}/Objects/references/` |
| Filename pattern | `YYYY-MM-DD-Xof10-slug.md` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.1 | 2025-12-16 | Previous version (auto-save) |
| 3.0 | 2025-12-18 | Added user-prefs support, configurable output mode |
