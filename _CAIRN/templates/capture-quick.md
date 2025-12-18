<%*
const subject = await tp.system.prompt("What's this about?");
if (!subject) {
    new Notice("No subject provided - canceling");
    return;
}

const slug = subject.toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/^-|-$/g, '')
    .substring(0, 40);

const timestamp = tp.date.now("YYYYMMDD-HHmmss");
const filename = `${timestamp}-${slug}`;
await tp.file.rename(filename);
await tp.file.move("Capture/" + filename);
-%>
---
title: "<% subject %>"
captured: <% tp.date.now("YYYY-MM-DD HH:mm") %>
type: capture
status: unprocessed
tags:
  - inbox
---

# <% subject %>

## Notes


## Next Actions
- 

## Process To
<!-- Where should this go? -->
<!-- Track: Tracks/area-xxx or Tracks/p###-xxxx-yyyy -->
<!-- Object: Objects/ -->
<!-- Delete: Not worth keeping -->
