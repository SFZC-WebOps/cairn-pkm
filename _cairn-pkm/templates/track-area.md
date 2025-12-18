<%*
const title = await tp.system.prompt("Area name (e.g., Finance, Health, WebOps)");
if (!title) {
    new Notice("No name provided - canceling");
    return;
}

const slug = title.toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/^-|-$/g, '');

const folderName = `area-${slug}`;
const fileName = `_${folderName}-home`;
const today = tp.date.now("YYYY-MM-DD");

// Create folder structure
await app.vault.createFolder(`Tracks/${folderName}`);
await app.vault.createFolder(`Tracks/${folderName}/resources`);
await app.vault.createFolder(`Tracks/${folderName}/tasks`);
await app.vault.createFolder(`Tracks/${folderName}/zzz`);

await tp.file.rename(fileName);
await tp.file.move(`Tracks/${folderName}/${fileName}`);
-%>
---
title: "<% title %>"
type: area
status: active
created: <% today %>
modified: <% today %>
tags:
  - area
---

## Overview

[What this area covers and why it matters]

## Current Focus

[What's active right now in this area]

## Standards

- [Standard to maintain]
- [Standard to maintain]

## Notes

[Working scratchpad — open questions, things to remember]

---

## Log

---

<% today %> - Created area

