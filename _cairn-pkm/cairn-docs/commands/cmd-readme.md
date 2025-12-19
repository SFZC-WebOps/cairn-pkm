# !readme — Reference Research

**Type:** Read + Web Search + Write | **Version:** 2.1

Analyze URLs with strategic assessment and usefulness scoring.

---

## Quick Reference

| Command | What Happens | Permission |
|---------|--------------|------------|
| `!readme` | Analyze URL in conversation | None |
| `!readme [url]` | Analyze specific URL | None |

---

## Workflow

1. Paste URL in conversation (or provide with command)
2. Type `!readme`
3. Get strategic analysis with usefulness score
4. File auto-saved to references folder

---

## What It Does

### Phase 1: Fetch & Extract
- Fetch web page content
- Extract title, author, date
- Identify content type

### Phase 2: Content Synthesis
- Parse key points
- Summarize in 3-5 sentences
- Identify content type (article, tool, docs, video, research, tutorial)

### Phase 3: Deep Context Research

**Required searches:**
1. Author credibility and background
2. Topic currency (is this current thinking?)
3. Competitive landscape (alternatives)

**Conditional searches:**
4. Known issues (if tool/product)
5. Implementation examples (if technical)
6. Criticisms (if theory/research)

### Phase 4: Strategic Fit Analysis

Evaluates against:
- Direct project application
- Skill development value
- Future planning relevance
- General enrichment

### Phase 5: Usefulness Scoring

**Score 1-10:**

| Factor | Points |
|--------|--------|
| Solves active problem | +3 |
| Relevant to priority area | +2 |
| Established expert author | +2 |
| Current (< 12 months) | +1 |
| Actionable steps | +1 |
| No better alternatives | +1 |
| Low effort to apply | +1 |
| Dated (> 2 years) | -1 |
| Unknown author | -1 |
| Already have equivalent | -1 |
| Tangential to work | -2 |
| High effort, low certainty | -2 |

**Score interpretation:**

| Score | Rating | Action |
|-------|--------|--------|
| 8-10 | 🔥 High Priority | Act on this soon |
| 5-7 | ✅ Worth Filing | Reference for future |
| 3-4 | 🤔 Maybe | Skim and decide |
| 1-2 | ❌ Skip | Not worth your time |

---

## Output Format

```markdown
# {descriptive_title}
**Source:** [url]
**Type:** article | tool | documentation | video | research | tutorial
**Author:** [name] | **Credibility:** established | emerging | unknown | questionable
**Published:** [date] | **Currency:** cutting edge | current | dated | obsolete

### Synopsis
[3-5 sentence summary]

### Context Research
**Author Background:** [Who they are]
**Field Position:** [How this ranks]
**Critical View:** [Limitations, counterarguments]

### Strategic Fit

| Factor | Assessment |
|--------|------------|
| **Relevant Projects** | track identifier or "None" |
| **Area Alignment** | area + why |
| **Timing** | Right now | Next quarter | Someday |
| **Effort to Apply** | Trivial | Moderate | Significant |
| **Risk if Ignored** | High | Medium | Low | None |

### Utility Score: [X]/10 [emoji]
**Scoring Breakdown:**
[+/- factors]

### Recommendation
**Action:** Act Now | File for Reference | Skim & Decide | Skip
**If Filing, Tags:** #tag #tag
**Next Step:** [specific action if score > 5]

---
✔ Saved to: references/{filename}
```

---

## Output Filename

Format: `YYYY-MM-DD-Xof10-slug.md`

**Example:**
`2025-12-18-7of10-ssl-automation-guide.md`

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | "⚠️ Could not fetch — check link" |
| Paywall | "⚠️ Content behind paywall. Working with preview..." Score -1 |
| No author/date | "Author: Unknown" — Credibility = unknown |
| Sparse search results | "Limited context — scoring less reliable" |

---

*Back to [Commands Index](index.md)*
