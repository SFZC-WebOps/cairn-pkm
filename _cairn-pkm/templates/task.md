<%*
const title = await tp.system.prompt("Task title");
if (!title) {
    new Notice("No title provided - canceling");
    return;
}

// Get current track context from file path or prompt
let project = "";
const currentPath = tp.file.path(true);
const trackMatch = currentPath.match(/Tracks\/((?:area-[^/]+|p\d{3}-[^/]+))/);
if (trackMatch) {
    project = trackMatch[1];
} else {
    project = await tp.system.prompt("Track (e.g., area-finance or p001-blog-migr)");
}

const priority = await tp.system.suggester(
    ["low", "medium", "high", "critical"],
    ["low", "medium", "high", "critical"]
) || "medium";

const status = await tp.system.suggester(
    ["active", "blocked", "waiting", "scheduled", "deferred"],
    ["active", "blocked", "waiting", "scheduled", "deferred"]
) || "active";

const dueDate = await tp.system.prompt("Due date (YYYY-MM-DD, optional)", "", true);

const slug = title.toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/^-|-$/g, '')
    .substring(0, 40);

const today = tp.date.now("YYYY-MM-DD");
const filename = `${today.replace(/-/g, '')}-${slug}`;

await tp.file.rename(filename);

// Move to track's tasks folder if we know the track
if (project) {
    await tp.file.move(`Tracks/${project}/tasks/${filename}`);
}
-%>
---
title: "<% title %>"
project: <% project %>
created_date: <% today %>
due_date: <% dueDate %>
assignee: 
priority: <% priority %>
status: <% status %>
phase: 
effort: 
---

## Task History

- <% today %>: Created task

---

## Subtasks

- [ ] 

---

## Notes

