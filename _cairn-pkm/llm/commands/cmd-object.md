# !object - Object Management
*Type: Display/Write | Version: 0.5.1 | Updated: 2025-12-19*

<!-- Before updating version: Read /mnt/project/VERSION-POLICY.md -->

## Quick Reference

| Command | Purpose | Permission |
|---------|---------|------------|
| `!object` | Show usage help | None |
| `!object-c {type}` | Create new object | Display only |
| `!object-e` | Edit existing object | Display only |

Objects are things you *have* or *manage* (accounts, devices, contacts), not things you *do* (tasks, projects).

---

## Requirements

**Assistant Capabilities:**
- File generation
- Template application
- Frontmatter parsing

**Vault Structure:**
- `Objects/` Ã¢â‚¬â€ Cross-cutting objects
- `Tracks/*/resources/` Ã¢â‚¬â€ Track-specific objects

---

## !object-c Ã¢â‚¬â€ Create Object

**Syntax:** `!object-c {type} [details]`

### Object Types

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

### Execution

1. Identify object type from input
2. Apply template for that type
3. Populate fields from provided details
4. Use `TBD` for any missing required fields
5. Generate complete file with frontmatter and body sections
6. Output per user preferences

### Output

```
CONSTRUCT: filepath = {destination path based on scope}
CONSTRUCT: content = {complete file with frontmatter and body}

CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

### Destination

User saves to appropriate location based on scope:

| Scope | Destination | Example |
|-------|-------------|---------|
| Cross-cutting (multiple tracks) | `{VAULT_PATH}/Objects/` | contacts, shared devices |
| Track-specific | `{VAULT_PATH}/Tracks/{track}/resources/` | credit cards Ã¢â€ â€™ area-finance |

**Guideline:** If referenced by multiple tracks or doesn't clearly belong to one area, put in `Objects/`. If only relevant to one track, put in that track's `resources/` folder.

---

## !object-e Ã¢â‚¬â€ Edit Object

**Syntax:** `!object-e` (user uploads file first)

### Supported Edits

- Update frontmatter fields
- Add log entries or notes
- Fix or add backlinks
- Update status (active Ã¢â€ â€™ inactive Ã¢â€ â€™ archived)
- Refresh `lastmod` date

### Execution

1. User uploads existing object file
2. User describes desired changes
3. Assistant applies edits
4. Output per user preferences

### Output

```
CONSTRUCT: filepath = {original file path}
CONSTRUCT: content = {modified file content}

CALL: OUTPUT_FILE(filepath, content)
```

See `cmd-output-behavior.md` for OUTPUT_FILE pattern.

### Example

```
User: !object-e
[uploads credit-card-chase-prime.md]
Update credit limit to $12,000 and add note: "December 2024 - CLI increase approved"

Assistant output: Complete file with:
- `credit-limit: "12000"` in frontmatter
- New entry in Notes section
- `lastmod:` date refreshed
```

---

## Object Frontmatter Standard

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

Type-specific fields are added based on the object type.

---

## Error Handling

| Situation | Response |
|-----------|----------|
| Unknown object type | List available types, ask user to clarify |
| Missing required fields | Populate with TBD, note in output |
| File not uploaded for !object-e | Prompt user to upload file |
| Conflicting information | Ask user to clarify before applying |

---

## File Paths

| Purpose | Path |
|---------|------|
| Cross-cutting objects | `{VAULT_PATH}/Objects/` |
| Track-specific objects | `{VAULT_PATH}/Tracks/{track}/resources/` |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 0.5.1 | 2025-12-19 | Renamed command from !obj to !object for clarity |
| 0.5.0 | 2025-12-19 | Reset to pre-release versioning (was 2.1) |
| 1.0 | Ã¢â‚¬â€ | Initial OGB version |
| 2.0 | 2025-12-15 | LLM-agnostic refactor |
| 2.1 | 2025-12-16 | Standardized format |
