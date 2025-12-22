# !readme - Reference Research
*Type: Read + Web Search + Write | Updated: 2025-12-19*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!readme` | Analyze URL in current conversation |
| `!readme [url]` | Fetch and analyze specific URL |

**Workflow:** Paste URL â†’ `!readme` â†’ Strategic analysis + utility score â†’ Output

**Location:** `{VAULT_PATH}/Objects/references/`

---

## Initialization

**Common:** Per `cmd-shared-patterns.md`

**Command-specific:** `references_folder = "Objects/references"`

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
1. "[author name]" â€” Credibility, affiliations
2. "[main topic] [current year]" â€” Current or outdated?
3. "[tool/concept] alternatives comparison" â€” What else exists

CONDITIONAL SEARCHES:
4. IF tool/product: "[product name] reviews problems"
5. IF technical: "[concept] implementation examples"
6. IF theory/research: "[concept] criticism"

SYNTHESIZE:
- Author credibility: established expert | emerging voice | unknown | questionable
- Field currency: cutting edge | current | dated | obsolete
- Competitive landscape: best in class | one of many | inferior options exist
```

### Phase 4: Strategic Analysis

```
EVALUATE AGAINST USER CONTEXT:
1. Direct project application â€” Solves current problem?
2. Skill development â€” Builds relevant capability?
3. Future planning â€” Informs upcoming decisions?
4. General enrichment â€” Interesting but no immediate use?

ASSESS:
- Timing: Right now | Next quarter | Someday | Never
- Effort to apply: Trivial | Moderate | Significant
- Risk if ignored: High | Medium | Low | None
```

### Phase 5: Utility Score

```
CALCULATE (1-10):

+3 Solves active problem in current project
+2 Directly relevant to known priority area
+2 Author is established expert
+1 Content is current (< 12 months old)
+1 Actionable (clear steps, not just theory)
+1 No significant better alternatives
+1 Low effort to apply

-1 Content is dated (> 2 years old)
-1 Author credibility unclear
-1 Already have equivalent knowledge/tool
-2 Tangential to actual work
-2 High effort, low certainty of payoff

INTERPRETATION:
8-10: ðŸ”¥ High Priority â€” Act on this soon
5-7:  âœ… Worth Filing â€” Reference for future
3-4:  ðŸ¤” Maybe â€” Skim and decide
1-2:  âŒ Skip â€” Not worth your time
```

### Phase 6: Output

```
GENERATE: filename = "{YYYY-MM-DD}-{score}of10-{slug}.md"
CONSTRUCT: filepath = {VAULT_PATH}/Objects/references/{filename}
CALL: OUTPUT_FILE(filepath, content)
```

Output varies by file_operations setting. See `cmd-output-behavior.md`.

**Complete:** Per `cmd-shared-patterns.md`

---

## Output Format

```markdown
# {descriptive_title}
**Source:** [url]
**Type:** [article|tool|documentation|video|research|tutorial|other]
**Author:** [name] | **Credibility:** [level]
**Published:** [date] | **Currency:** [level]

### Synopsis
[3-5 sentences]

### Context Research
**Author Background:** [Who, experience, notable work]
**Field Position:** [Alternatives, competitive landscape]
**Critical View:** [Criticisms, limitations, counterarguments]

### Strategic Fit

| Factor | Assessment |
|--------|------------|
| **Relevant Projects** | [track or "None directly"] |
| **Area Alignment** | [area + why] |
| **Timing** | [Right now | Next quarter | Someday] |
| **Effort to Apply** | [Trivial | Moderate | Significant] |
| **Risk if Ignored** | [High | Medium | Low | None] |

### Utility Score: [X]/10 [emoji]
**Scoring Breakdown:** [+/- factors]

### Recommendation
**Action:** [Act Now | File for Reference | Add to Track | Skim & Decide | Skip]
**If Filing, Tags:** #tag #tag #tag
**Next Step:** [Specific action if score > 5]
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | "Could not fetch [url] â€” check link or try again" |
| Paywall/login required | "Content behind paywall. Working with preview..." Score -1 |
| No author/date found | "Author: Unknown | Published: Unknown" â€” Credibility = unknown |
| Search results sparse | "Limited context â€” scoring may be less reliable" |

Common errors: See `cmd-shared-patterns.md`
