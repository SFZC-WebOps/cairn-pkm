<%*
const title = await tp.system.prompt("Project name");
if (!title) {
    new Notice("No name provided - canceling");
    return;
}

// Get next project number
const tracks = app.vault.getAbstractFileByPath("Tracks");
let maxNum = 0;
if (tracks && tracks.children) {
    for (const child of tracks.children) {
        const match = child.name.match(/^p(\d{3})-/);
        if (match) {
            const num = parseInt(match[1]);
            if (num > maxNum) maxNum = num;
        }
    }
}
const nextNum = String(maxNum + 1).padStart(3, '0');

const systemCode = await tp.system.prompt("System code (4 letters, e.g., blog, home, work)");
const actionCode = await tp.system.prompt("Action code (4 letters, e.g., migr, setup, audt)");

const projectId = `p${nextNum}-${systemCode.toLowerCase()}-${actionCode.toLowerCase()}`;
const fileName = `_${projectId}-home`;
const today = tp.date.now("YYYY-MM-DD");

// Create folder structure
await app.vault.createFolder(`Tracks/${projectId}`);
await app.vault.createFolder(`Tracks/${projectId}/resources`);
await app.vault.createFolder(`Tracks/${projectId}/tasks`);
await app.vault.createFolder(`Tracks/${projectId}/zzz`);

await tp.file.rename(fileName);
await tp.file.move(`Tracks/${projectId}/${fileName}`);
-%>
---
project_id: <% projectId %>
title: "<% title %>"
type: project
status: active
progress: 0
created: <% today %>
modified: <% today %>
tags:
  - project
---

## Overview

[What this project accomplishes and why]

## Success Criteria

- [ ] [Measurable outcome]
- [ ] [Measurable outcome]
- [ ] [Measurable outcome]

## Current Focus

[What's active right now]

## Notes

[Working scratchpad — open questions, pending decisions]

---

## Log

---

<% today %> - Created project

