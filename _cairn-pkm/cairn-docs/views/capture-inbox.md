# capture-inbox View

Inbox processing status.

---

## Location

`_cairn-pkm/views/capture-inbox.md`

---

## Sections

### Unprocessed Items

Table of items in `Capture/`:
- Item link
- Captured date
- Days old

```dataview
TABLE
  file.link as "Item",
  file.ctime as "Captured",
  round((date(today) - file.ctime).days) as "Days Old"
FROM "Capture"
WHERE status = "unprocessed" OR !status
SORT file.ctime ASC
```

### Inbox Stats

Summary:
- ✅ Inbox Zero! (if empty)
- 📥 N items in inbox
- ⚠️ N items older than 7 days

---

## Processing Guidance

Each capture item should become one of:
- **Task** → Move to `Tracks/*/tasks/`
- **Reference** → Move to `Objects/` or `Tracks/*/resources/`
- **Delete** → Not worth keeping

---

## Use Cases

- Daily inbox check
- Weekly review processing
- GTD capture workflow

---

*Back to [Views Index](index.md)*
