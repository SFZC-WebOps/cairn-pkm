# !object - Object Management
*Type: Display/Write | Version: 0.6.0 | Updated: 2025-12-19*

## Quick Reference

| Command | Purpose |
|---------|---------|
| `!object` | Show usage help |
| `!object-c {type}` | Create new object |
| `!object-e` | Edit existing object (upload first) |

Objects are things you *have* or *manage* (accounts, devices, contacts), not things you *do* (tasks, projects).

---

## Object Types

| Type | Filename Pattern | Example |
|------|------------------|---------|
| contact | `contact-{firstname-lastname}.md` | `contact-hazel-frost.md` |
| credit-card | `credit-card-{issuer}-{name}.md` | `credit-card-chase-sapphire.md` |
| account | `account-{institution}-{type}.md` | `account-chase-checking.md` |
| loan | `loan-{lender}.md` | `loan-lendingclub.md` |
| utility | `utility-{provider}.md` | `utility-pge.md` |
| telecom | `telecom-{provider}.md` | `telecom-google-fi.md` |
| subscription | `subscription-{service}.md` | `subscription-netflix.md` |
| device | `device-{type}-{identifier}.md` | `device-laptop-thinkpad.md` |
| medication | `medication-{name}.md` | `medication-lisinopril.md` |
| provider | `provider-{name}.md` | `provider-dr-smith.md` |

---

## !object-c — Create Object

**Syntax:** `!object-c {type} [details]`

### Execution

1. Identify object type from input
2. Apply template for that type
3. Populate fields from provided details
4. Use `TBD` for missing required fields
5. Output per file_operations setting

### Destination

| Scope | Location |
|-------|----------|
| Cross-cutting (multiple tracks) | `{VAULT_PATH}/Objects/` |
| Track-specific | `{VAULT_PATH}/Tracks/{track}/resources/` |

**Guideline:** If referenced by multiple tracks, use `Objects/`. If only relevant to one track, use that track's `resources/`.

Output per file_operations setting. See `cmd-output-behavior.md`.

---

## !object-e — Edit Object

**Syntax:** `!object-e` (user uploads file first)

### Supported Edits

- Update frontmatter fields
- Add log entries or notes
- Update status (active → inactive → archived)
- Refresh `lastmod` date

### Execution

1. User uploads existing object file
2. User describes desired changes
3. Assistant applies edits
4. Output per file_operations setting

---

## Frontmatter Standard

All objects share these common fields:

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

Type-specific fields added based on object type.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Unknown object type | List available types, ask to clarify |
| Missing required fields | Populate with TBD, note in output |
| File not uploaded (!object-e) | Prompt user to upload file |
| Conflicting information | Ask user to clarify |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.6.0 | 2025-12-19 | Streamlined format |
| 0.5.1 | 2025-12-19 | Renamed from !obj to !object |
| 0.5.0 | 2025-12-19 | Reset to pre-release versioning |
