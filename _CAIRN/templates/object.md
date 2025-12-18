<%*
const objectTypes = [
    "contact",
    "vendor", 
    "tool",
    "account",
    "subscription",
    "device",
    "location",
    "other"
];

const selectedType = await tp.system.suggester(objectTypes, objectTypes);
if (!selectedType) {
    new Notice("No type selected - canceling");
    return;
}

let title, extraFields = "";
const today = tp.date.now("YYYY-MM-DD");

switch(selectedType) {
    case "contact":
        const firstName = await tp.system.prompt("First name");
        const lastName = await tp.system.prompt("Last name");
        title = `${firstName} ${lastName}`;
        extraFields = `
## Contact

| Field | Value |
|-------|-------|
| Phone | |
| Email | |
| Company | |
| Role | |

## Relationship

- How we met: 
- Context: 
- Last contact: 

## Notes

`;
        break;
        
    case "vendor":
        title = await tp.system.prompt("Vendor/Company name");
        extraFields = `
## Contact

| Field | Value |
|-------|-------|
| Phone | |
| Website | |
| Email | |
| Account # | |

## Service Details

- What they provide: 
- Contract terms: 
- Cost: 

## Notes

`;
        break;
        
    case "tool":
        title = await tp.system.prompt("Tool/Software name");
        extraFields = `
## Details

| Field | Value |
|-------|-------|
| Version | |
| License | |
| Website | |
| Cost | |

## Usage

- Purpose: 
- Where used: 
- Alternatives: 

## Notes

`;
        break;
        
    case "account":
        title = await tp.system.prompt("Account name");
        extraFields = `
## Account Details

| Field | Value |
|-------|-------|
| Provider | |
| Username | |
| Account # | |
| Website | |

## Access

- Login method: 
- 2FA: 
- Recovery: 

## Notes

`;
        break;
        
    case "subscription":
        title = await tp.system.prompt("Subscription name");
        extraFields = `
## Subscription Details

| Field | Value |
|-------|-------|
| Provider | |
| Plan | |
| Cost | |
| Billing cycle | |
| Renewal date | |

## Access

- Website: 
- Login: 

## Notes

`;
        break;
        
    case "device":
        title = await tp.system.prompt("Device name/description");
        extraFields = `
## Device Details

| Field | Value |
|-------|-------|
| Make | |
| Model | |
| Serial # | |
| Purchase date | |
| Warranty until | |

## Location

- Where: 
- Condition: 

## Notes

`;
        break;
        
    case "location":
        title = await tp.system.prompt("Location name");
        extraFields = `
## Address

| Field | Value |
|-------|-------|
| Street | |
| City | |
| State/ZIP | |
| Phone | |

## Details

- Type: 
- Hours: 
- What I use it for: 

## Notes

`;
        break;
        
    default:
        title = await tp.system.prompt("Object name");
        extraFields = `
## Details

| Field | Value |
|-------|-------|
| | |
| | |

## Notes

`;
}

const slug = title.toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/^-|-$/g, '');

const filename = `${selectedType}-${slug}`;
await tp.file.rename(filename);
await tp.file.move(`Objects/${filename}`);
-%>
---
title: "<% title %>"
type: object
object_type: <% selectedType %>
status: active
created: <% today %>
lastmod: <% today %>
tags:
  - <% selectedType %>
---

# <% title %>
<% extraFields %>
---

## Log

---

<% today %> - Created object

