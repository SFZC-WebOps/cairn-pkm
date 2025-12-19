# !obj — Object Management

**Type:** Display/Write | **Version:** 2.1

Create and edit universal objects (contacts, vendors, tools, accounts, etc.)

---

## Quick Reference

| Command | Purpose | Permission |
|---------|---------|------------|
| `!obj` | Show usage help | None |
| `!obj-c {type}` | Create new object | Display only |
| `!obj-e` | Edit existing object | Display only |

---

## What Are Objects?

Objects are things you *have* or *manage*, not things you *do* (those are tasks).

**Examples:**
- Contacts (people)
- Vendors (service providers)
- Tools (software)
- Accounts (credentials)
- Subscriptions (recurring services)
- Devices (hardware)
- Locations (places)

---

## !obj-c — Create Object

### Syntax

`!obj-c {type} [details]`

### Available Types

| Type | Filename Pattern | Example |
|------|------------------|---------|
| contact | `contact-{firstname-lastname}.md` | `contact-jane-smith.md` |
| vendor | `vendor-{company}.md` | `vendor-acme-hosting.md` |
| tool | `tool-{name}.md` | `tool-obsidian.md` |
| account | `account-{institution}-{type}.md` | `account-chase-checking.md` |
| subscription | `subscription-{service}.md` | `subscription-netflix.md` |
| device | `device-{type}-{identifier}.md` | `device-laptop-thinkpad.md` |
| location | `location-{name}.md` | `location-downtown-office.md` |
| credit-card | `credit-card-{issuer}-{name}.md` | `credit-card-chase-sapphire.md` |
| loan | `loan-{lender}.md` | `loan-lendingclub.md` |
| utility | `utility-{provider}.md` | `utility-pge.md` |
| telecom | `telecom-{provider}.md` | `telecom-google-fi.md` |
| medication | `medication-{name}.md` | `medication-lisinopril.md` |
| provider | `provider-{name}.md` | `provider-dr-smith.md` |

### Execution

1. Identify object type from input
2. Apply template for that type
3. Populate fields from provided details
4. Use `TBD` for missing required fields
5. Display complete file for user to save locally

---

## !obj-e — Edit Object

### Workflow

1. Upload existing object file
2. Describe desired changes
3. Type `!obj-e`
4. Review modified file
5. Save locally

### Supported Edits

- Update frontmatter fields
- Add log entries or notes
- Fix or add backlinks
- Update status (active → inactive → archived)
- Refresh `lastmod` date

---

## Where to Save Objects

| Scope | Destination |
|-------|-------------|
| Cross-cutting (multiple tracks) | `Objects/` |
| Track-specific | `Tracks/{track}/resources/` |

**Guideline:** If referenced by multiple tracks or doesn't clearly belong to one area, put in `Objects/`.

---

## Object Frontmatter

All objects share common fields:

```yaml
---
type: {object-type}
status: active | inactive | archived
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
aliases: []
tags: []
---
```

Type-specific fields are added based on the object type.

---

## Type-Specific Templates

### Contact

```yaml
---
type: contact
status: active
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
---

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
```

### Vendor

```yaml
---
type: vendor
status: active
---

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
```

### Tool

```yaml
---
type: tool
status: active
---

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
```

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Unknown type | List available types, ask to clarify |
| Missing required | Populate with TBD, note in output |
| File not uploaded | Prompt to upload |
| Conflicting info | Ask to clarify before applying |

---

*Back to [Commands Index](index.md)*
