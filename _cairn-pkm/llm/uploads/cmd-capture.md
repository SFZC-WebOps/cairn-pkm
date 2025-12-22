# !capture - Quick Capture
*Type: Read/Write | Updated: 2025-12-21*

## Quick Reference

| Command | What Happens |
|---------|--------------|
| `!capture` | Capture from conversation context (URL or insight) |
| `!capture [topic]` | Capture with explicit topic focus |

**Workflow:** `!capture` â†’ Auto-detect content type â†’ Extract â†’ Save to Capture/

---

## Initialization

Per `cmd-shared-patterns.md`

---

## Execution

### Phase 1: Display DateTime

Per `cmd-shared-patterns.md`

### Phase 2: Detect Content Type

```
SCAN conversation for:
  1. URL presence â†’ URL_CAPTURE
  2. No URL â†’ INSIGHT_CAPTURE
```

### Phase 3: Extract

**URL_CAPTURE:**
```
FETCH: URL content
EXTRACT:
  - title: Page title
  - source: Domain
  - description: Meta description or first paragraph (2-3 sentences max)
  - capture_reason: Why this came up in conversation
```

**INSIGHT_CAPTURE:**
```
EXTRACT:
  - topic: Main subject discussed
  - key_points: Core insights (3-5 bullets max)
  - decisions: Any decisions made (if applicable)
  - capture_reason: What prompted capturing this
```

### Phase 4: Generate File

```
FILENAME: cap-{YYYY}-{MM}-{DD}-{HHMMSS}-{slug}.md
PATH: {VAULT_PATH}/Capture/{filename}

# Apply encoding fix (see cmd-shared-patterns.md)
content = ftfy.fix_text(content)
```

### Phase 5: Propose & Confirm

```
OUTPUT: "ðŸ“‹ PROPOSED CAPTURE"
OUTPUT: "â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•"
OUTPUT: "Filename: {filename}"
OUTPUT: "Path: {filepath}"
OUTPUT: ""
OUTPUT: {content}
OUTPUT: "â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•â•"
OUTPUT: ""
OUTPUT: "Save this? (yes / edit / cancel)"

WAIT FOR: response

SWITCH response:
  CASE "yes" | "y" | "save":
    PROCEED to Phase 6
  CASE "edit":
    PROMPT: "What would you like to change?"
    APPLY changes
    REPEAT Phase 5
  CASE "cancel" | "no" | "n":
    OUTPUT: "Capture cancelled."
    STOP
```

### Phase 6: Output

```
# VBM: Verify before modify
VERIFY: Capture/ directory exists
STATE: "Creating {filename} in Capture/"

CALL: OUTPUT_FILE(filepath, content)
```

Output varies by file_operations setting. See `cmd-output-behavior.md`.

**Complete:** Per `cmd-shared-patterns.md`

---

## Templates

### URL Capture

```markdown
# {title}

**Source:** {url}
**Captured:** {YYYY-MM-DD HH:MM}

## Description
{description}

## Why Captured
{capture_reason}

## Notes

---
```

### Insight Capture

```markdown
# {topic}

**Captured:** {YYYY-MM-DD HH:MM}

## Key Points
- {point}
- {point}
- {point}

## Decisions
{decisions or "None"}

## Why Captured
{capture_reason}

## Notes

---
```

---

## Slug Generation

**Rules:**
- 3-4 key words from title/topic
- Lowercase, hyphenated
- Max 40 characters
- Remove articles (a, an, the)

**Examples:**
- "How to Configure SSL Certificates" â†’ `ssl-certificates-config`
- "Discussion about Q1 budget priorities" â†’ `q1-budget-priorities`

---

## Capture Reason Extraction (RC Principle)

The "Why Captured" field implements Rationale Capture (RC) - preserving the *why* so future-you understands context. Extract from conversation:

| Conversation Pattern | Extracted Reason |
|---------------------|------------------|
| "This looks useful for..." | Direct quote of intent |
| Link shared without comment | "Referenced in discussion about {topic}" |
| "We should remember that..." | What follows |
| Insight after problem-solving | "Learned while working on {problem}" |
| No clear reason | "Captured from {brief context}" |

**Never leave blank.** Even minimal context ("Came up in session") is better than nothing.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| URL unreachable | Save with URL and note: "Could not fetch - site unreachable" |
| Paywall/login required | Save with available meta info, note: "Content behind paywall" |
| Empty conversation | "Nothing to capture. What should I save?" |
| URL + insights both present | Prioritize URL capture, include insights in Notes section |
| Write fails | Fall back per GFC (write â†’ download â†’ display) |

Common errors: See `cmd-shared-patterns.md`

---

## Integration

| Works With | How |
|------------|-----|
| !hi | Captures can reference track context if mentioned |
| !bye | Logs captures in session summary |
| !create task | User can convert capture to task during review |
| Inbox dashboard | Dataview queries Capture/ for unprocessed items |
