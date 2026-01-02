# Quick Capture
*Type: Read/Write | Updated: 2025-01-01*

## Overview

Capture ideas, links, and insights from conversation quickly. Saves to Capture/ for later processing.

**Shortcut:** `!capture`

---

## Natural Capture

### From Conversation Context

```
User: Oh, that article about SSL certificates was really useful

LLM: Want me to capture that for later?

User: Yes

LLM: [Creates capture file]

     ✓ Captured to: cap-2025-01-01-143022-ssl-certificates.md
     
     Title: SSL Certificate Best Practices
     Source: https://example.com/ssl-guide
     Why captured: Referenced in security discussion
```

### URL Capture

When a URL is shared:

```
User: Check out this article: https://example.com/productivity-tips

LLM: Want me to capture that link?

User: Yes, I want to read it later

LLM: [Fetches page, extracts details]

     Capturing:
     - Title: 10 Productivity Tips for 2025
     - Source: example.com
     - Why: To read later
     
     Save this?
```

### Insight Capture

When discussing something worth remembering:

```
User: We decided to use monthly billing instead of annual

LLM: That sounds like a decision worth capturing. Want me to save it?

User: Yes

LLM: Capturing:
     - Topic: Billing frequency decision
     - Decision: Monthly billing instead of annual
     - Context: [from conversation]
     
     Save this?
```

---

## Shortcut: !capture

Immediately scans conversation and captures:

```
!capture
```

**Behavior:**
1. Scans recent conversation for URLs or key insights
2. Extracts relevant content
3. Shows draft capture
4. Confirms before saving

---

## Capture Format

### URL Capture

```markdown
---
type: capture
capture_type: url
title: "{page title}"
source: "{url}"
captured: 2025-01-01
processed: false
tags: []
---

# {title}

**Source:** {url}
**Captured:** 2025-01-01 14:30

## Description
{meta description or first paragraph}

## Why Captured
{reason from conversation}

## Notes

---
```

### Insight Capture

```markdown
---
type: capture
capture_type: insight
title: "{topic}"
captured: 2025-01-01
processed: false
tags: []
---

# {topic}

**Captured:** 2025-01-01 14:30

## Key Points
- {point}
- {point}

## Decisions
{decisions or "None"}

## Why Captured
{capture reason}

## Notes

---
```

---

## File Naming

**Format:** `cap-{YYYY}-{MM}-{DD}-{HHMMSS}-{slug}.md`

**Slug rules:**
- 3-4 key words from title/topic
- Lowercase, hyphenated
- Max 40 characters

**Examples:**
- cap-2025-01-01-143022-ssl-certificates.md
- cap-2025-01-01-150000-billing-decision.md

---

## Why Captured (RC Principle)

Always capture *why* something is being saved:

```
Good: "Referenced while researching security options"
Good: "Decision made during budget planning"
Bad:  "Looked useful"
```

Extract from conversation context — what prompted this capture?

---

## Output Behavior

After confirmation:

1. Check prefs for file_operations
2. Save to Capture/ directory
3. Report success

```
LLM: ✓ Captured to Capture/cap-2025-01-01-143022-ssl-certificates.md
```

---

## Processing Later

Captures have `processed: false` in frontmatter. During reviews:

1. Open Capture/ folder
2. For each unprocessed item, decide:
   - Convert to task
   - Create object
   - Move to project resources
   - Delete (not useful)
3. Mark as `processed: true` or delete

---

## Error Recovery

| Situation | Response |
|-----------|----------|
| URL unreachable | "Couldn't fetch that page. I'll save the URL anyway with a note." |
| Paywall/login | "That's behind a paywall. I'll save what I can see." |
| Nothing to capture | "What would you like me to save?" |
| Save fails | Fall back to download, show content |
