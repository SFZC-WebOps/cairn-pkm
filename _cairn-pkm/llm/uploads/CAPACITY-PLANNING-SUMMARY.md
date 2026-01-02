# Capacity Planning Feature Summary

*Captures the design discussion for capacity planning in Cairn-PKM.*

*Sessions: 2026-01-01 (two sessions totaling ~2 hours)*

---

## The Problem

User has a meeting with their boss to map out the upcoming year. Needed a way to:
- Visualize time commitments across projects
- Identify overcommitment periods
- Show which projects conflict and why
- Support data-grounded planning conversations

---

## Solution: New Frontmatter Fields + !capacity Command

### New Project Frontmatter Fields

Added to project home documents (`_p###-*-home.md`):

```yaml
# Capacity planning fields (projects)
effort_percent: 0             # 0-100, your time commitment (20 = one day/week)
peak_start: YYYY-MM-DD        # When intense phase begins
peak_end: YYYY-MM-DD          # When intense phase ends
stakeholder: ""               # Whose priority (name, department, "self")
category: operations          # operations | development | planning | event | compliance
flexibility: negotiable       # fixed | negotiable | flexible
recurrence: ""                # For cyclical work: "annual", "quarterly", "Q4"
```

**Key insight:** `effort_percent` is your *personal* time commitment (20% = ~1 day/week), not project size or headcount.

**Flexibility matters:** 
- `fixed` = Hard deadline, cannot move (audits, conferences, compliance)
- `negotiable` = Could adjust with stakeholder approval
- `flexible` = You control timing

---

## New !capacity Command

### Views Available

| Command | Purpose |
|---------|---------|
| `!capacity` | Current year overview with monthly breakdown |
| `!capacity [year]` | Specific year |
| `!capacity Q1\|Q2\|Q3\|Q4` | Quarter detail with weekly view |
| `!capacity stakeholder` | Group by who's driving the work |
| `!capacity conflicts` | Show only overcommitted periods |
| `!capacity plan` | Interactive "what-if" simulator |
| `!capacity report` | Generate downloadable report for leadership |

### Design Principle

**"Always show project names and titles, not just numbers."**

Numbers are meaningless without knowing what's behind them. Every view shows:
- Project ID and title
- The `summary` field (editorial insight on current state)
- Who's the stakeholder
- Whether it can be moved

### Key Output Features

**Monthly view with peaks:**
```
### April: 155% → 194% with peaks 🔴🔴 CRITICAL
| Project | Base | Peak | Status |
|---------|------|------|--------|
| p001-cmpl-audt | 35% | **52%** | Auditors on-site — **PEAK** |
| p002-site-rdsg | 25% | **37%** | Website — **PEAK** |
...

**What's driving this:** Everything converges. Audit peak + Conference final prep...

**Flexibility analysis:**
- 🔒 Fixed (55%): Audit + Conference — cannot move
- 🔄 Negotiable (60%): Website + Training + Budget
- ⏸️ Flexible (40%): Infrastructure + Docs
```

**Resolution options:**
```
**Option 1: Defer Infrastructure to July** (easiest)
- Move p004-infr-upgr start from May → July
- Saves: 30% in Apr–May
- Result: 125% base (still over, but survivable)
- Trade-off: IT work pushed to Q3
```

---

## Interactive Planning Mode (`!capacity plan`)

For boss meetings — simulate changes without committing:

```
📊 CAPACITY PLANNING SESSION
═══════════════════════════════════════════════════════════════

Starting point: April at 155% base / 194% peak

What would you like to try?
- "defer p004 to July"
- "compress p002 to end in April"
- "what if we skip the conference"
- "show me options"
- "done" to exit
```

LLM interprets natural language, shows impact, lets user explore scenarios.

**Nothing commits until you explicitly say so.**

---

## Leadership Report (`!capacity report`)

Generates a downloadable markdown file formatted for sharing:
- Executive summary with key dates
- Visual timeline
- Risk assessment
- Stakeholder breakdown
- Recommended actions

Designed to be copy-pasted into emails or presentations.

---

## Files Modified

1. **_ARCHITECTURE.md** — Added capacity fields to project frontmatter
2. **cmd-shared-patterns.md** — Added enums for category, flexibility
3. **cmd-create.md** — Updated project prompts and template
4. **cmd-edit.md** — Added project capacity field commands
5. **cmd-capacity.md** (NEW) — Complete command specification (~1000 lines)

---

## Key Design Decisions

1. **Effort is personal time, not project size** — 20% means you spend ~1 day/week on it, regardless of how many people are involved overall.

2. **Peaks are separate from base effort** — Projects aren't uniform intensity. A 20% project might spike to 40% during its crunch phase.

3. **Summary field for editorial context** — "Auditors arriving Monday" tells you more than "35% effort" about why April is critical.

4. **Flexibility drives resolution options** — Fixed deadlines constrain what can move; the command surfaces this automatically.

5. **Interactive planning doesn't commit** — Boss meetings involve hypotheticals. The simulator lets you explore without changing anything.

---

## Example Use Case

**Scenario:** Q2 shows 155% base load, 194% with peaks

**What the analysis reveals:**
- Audit (fixed, 35%) + Conference (fixed, 20%) = 55% immovable
- Website (negotiable, 25%) + Training (negotiable, 20%) + Budget (negotiable, 15%) = 60% moveable
- Infrastructure (flexible, 30%) + Docs (flexible, 10%) = 40% fully flexible

**Resolution:** Defer Infrastructure + pause Docs → April drops to 115%, manageable

---

## Transcripts

- `2026-01-01-03-08-27-capacity-planning-feature-design.txt` — Initial design, frontmatter fields, demo output
- `2026-01-01-04-08-39-capacity-planning-command-design.txt` — Command specification, interactive planning, report generation

---

*This feature enables data-grounded capacity conversations instead of going from memory.*
