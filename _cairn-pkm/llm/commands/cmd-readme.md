# !readme - Reference Research
*Type: Read + Web Search + Write | Version: 0.6.0 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | What Happens | Output Mode |
|---------|--------------|-------------|
| `!readme` | Analyze URL in current conversation | Per cairn-pkm-user-prefs.yaml |
| `!readme [url]` | Fetch and analyze specific URL | Per cairn-pkm-user-prefs.yaml |

**Workflow:** Paste URL Ã¢â€ â€™ `!readme` Ã¢â€ â€™ Get strategic analysis and score Ã¢â€ â€™ Output per prefs

---

## Requirements

**Assistant Capabilities:**
- Web page fetching
- Web search (for context research)
- File system access (if file_operations = write or confirm)
- User context awareness (projects, interests)

**User Configuration:**
- `/mnt/project/cairn-pkm-user-prefs.yaml` Ã¢â‚¬â€ file_operations setting, references_folder

**Vault Structure:**
- References folder for saving analyzed content (per references_folder setting)

---

## Initialization

```
READ: /mnt/project/cairn-pkm-user-prefs.yaml
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
1. "[author name]" Ã¢â‚¬â€ Who is this person, credibility, affiliations
2. "[main topic] [current year]" Ã¢â‚¬â€ Is this current or outdated thinking
3. "[tool/concept] alternatives comparison" Ã¢â‚¬â€ What else exists

CONDITIONAL SEARCHES:
4. IF tool/product: "[product name] reviews problems" Ã¢â‚¬â€ Known issues
5. IF technical: "[concept] implementation examples" Ã¢â‚¬â€ Practical application
6. IF theory/research: "[concept] criticism" Ã¢â‚¬â€ Counterarguments

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
1. Direct project application Ã¢â‚¬â€ Does this solve a current problem?
2. Skill development Ã¢â‚¬â€ Does this build relevant capability?
3. Future planning Ã¢â‚¬â€ Does this inform upcoming decisions?
4. General enrichment Ã¢â‚¬â€ Interesting but no immediate use?

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
8-10: Ã°Å¸â€Â¥ High Priority Ã¢â‚¬â€ Act on this soon
5-7:  Ã¢Å“â€¦ Worth Filing Ã¢â‚¬â€ Reference for future
3-4:  Ã°Å¸Â¤â€ Maybe Ã¢â‚¬â€ Skim and decide
1-2:  Ã¢ÂÅ’ Skip Ã¢â‚¬â€ Not worth your time
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
Ã°Å¸â€œâ€ž FILE CONTENT
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Filename: 2025-12-18-7of10-api-design-patterns.md
Path: {VAULT_PATH}/Objects/references/

{complete report content}

Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Copy this content and save to the path above.

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

### Completion (write mode)

```
Ã¢Å“â€œ Created {VAULT_PATH}/Objects/references/2025-12-18-7of10-api-design-patterns.md

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

### Completion (confirm mode)

```
Ã°Å¸â€œâ€ž PROPOSED FILE
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Filename: 2025-12-18-7of10-api-design-patterns.md
Path: {VAULT_PATH}/Objects/references/

{complete report content}

Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Write this file? (yes/no)
```

Then on confirmation:

```
Ã¢Å“â€œ Created {VAULT_PATH}/Objects/references/2025-12-18-7of10-api-design-patterns.md

Ã¢Å“â€œ Task complete
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
Ã°Å¸Â¤â€“ Waiting for next instruction
Ã¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢ÂÃ¢â€¢Â
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | "Ã¢Å¡Â Ã¯Â¸Â Could not fetch [url] Ã¢â‚¬â€ check link or try again" |
| Paywall/login required | "Ã¢Å¡Â Ã¯Â¸Â Content behind paywall. Working with available preview..." Score -1 |
| No author/date found | "Author: Unknown | Published: Unknown" Ã¢â‚¬â€ Credibility = unknown |
| Search results sparse | "Limited context available Ã¢â‚¬â€ scoring may be less reliable" |
| cairn-pkm-user-prefs.yaml missing | Use defaults: display mode, local target |
| Write fails (write/confirm mode) | Report error, fall back to display mode |

---

## File Paths

| Purpose | Path |
|---------|------|
| User prefs | `/mnt/project/cairn-pkm-user-prefs.yaml` |
| Output | `{VAULT_PATH}/Objects/references/` |
| Filename pattern | `YYYY-MM-DD-Xof10-slug.md` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.6.0 | 2025-12-19 | Reset to pre-release versioning (was 3.0) |
| 3.0 | 2025-12-18 | Added user-prefs support, configurable output mode |
| 2.1 | 2025-12-16 | Previous version (auto-save) |
