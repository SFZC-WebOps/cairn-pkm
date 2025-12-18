# Inbox Status

Quick view of unprocessed items in `Capture/`.

## Unprocessed Items

```dataview
TABLE WITHOUT ID
  file.link as "Item",
  file.ctime as "Captured",
  round((date(today) - file.ctime).days) as "Days Old"
FROM "Capture"
WHERE status = "unprocessed" OR !status
SORT file.ctime ASC
```

## Inbox Stats

```dataviewjs
const items = dv.pages('"Capture"');
const count = items.length;

if (count === 0) {
    dv.paragraph("✅ **Inbox Zero!**");
} else {
    const oldItems = items.filter(p => {
        const age = (new Date() - new Date(p.file.ctime)) / (1000 * 60 * 60 * 24);
        return age > 7;
    }).length;
    
    dv.paragraph(`📥 **${count}** items in inbox`);
    if (oldItems > 0) {
        dv.paragraph(`⚠️ **${oldItems}** items older than 7 days`);
    }
}
```

## Quick Actions

- Process each item during weekly review
- Move to appropriate Track or Objects
- Delete if not worth keeping
