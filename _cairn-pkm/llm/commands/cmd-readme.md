# !readme - Reference Research
*Type: Read + Web Search + Write | Version: 2.1 | Updated: 2025-12-16*

## Quick Reference

| Command | What Happens | Permission |
|---------|--------------|------------|
| `!readme` | Analyze URL in current conversation | None |
| `!readme [url]` | Fetch and analyze specific URL | None |

**Workflow:** Paste URL → `!readme` → Get strategic analysis and score → Auto-saves to references folder

---

## Requirements

**Assistant Capabilities:**
- Web page fetching
- Web search (for context research)
- File system write access
- User context awareness (projects, interests)

**Vault Structure:**
- References folder for saving analyzed content (e.g., `xLab/references/` or `Objects/references/`)

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

### Phase 6: Output & Save
```
GENERATE filename:
  score = UTILITY_SCORE
  slug = extract_slug(title, 4-5 words, lowercase, hyphenated, max 40 chars)
  filename = "{YYYY-MM-DD}-{score}of10-{slug}.md"
  path = {VAULT_PATH}/{references_folder}/{filename}

CREATE file at path with report content
```

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

---
✓ Saved to: {references_folder}/{filename}
🤖 Waiting for next instruction
---
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | "⚠️ Could not fetch [url] — check link or try again" |
| Paywall/login required | "⚠️ Content behind paywall. Working with available preview..." Score -1 |
| No author/date found | "Author: Unknown \| Published: Unknown" — Credibility = unknown |
| Search results sparse | "Limited context available — scoring may be less reliable" |

---

## File Paths

| Purpose | Path |
|---------|------|
| Output | `{VAULT_PATH}/{references_folder}/` |
| Filename pattern | `YYYY-MM-DD-Xof10-slug.md` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | 2025-12-05 | Initial version |
| 2.0 | 2025-12-15 | LLM-agnostic refactor |
| 2.1 | 2025-12-16 | Standardized format |
