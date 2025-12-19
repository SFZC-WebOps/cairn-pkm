# object Template

Create a universal object with type-specific fields.

---

## Prompts

| Prompt | Purpose |
|--------|---------|
| Object type | Select from: contact, vendor, tool, account, subscription, device, location, other |
| Type-specific prompts | Varies by type |

---

## Output

**Filename:** `{type}-{slug}.md`

**Location:** `Objects/`

---

## Available Types

### contact

Creates a person record.

**Additional prompts:** First name, Last name

```markdown
---
title: "{First Last}"
type: object
object_type: contact
status: active
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
tags:
  - contact
---

# {First Last}

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

---

### vendor

Creates a service provider record.

**Additional prompts:** Vendor/Company name

```markdown
---
title: "{name}"
type: object
object_type: vendor
status: active
---

# {name}

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

---

### tool

Creates a software/utility record.

**Additional prompts:** Tool/Software name

```markdown
---
title: "{name}"
type: object
object_type: tool
status: active
---

# {name}

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

### account

Creates an account/credentials record.

**Additional prompts:** Account name

```markdown
---
title: "{name}"
type: object
object_type: account
status: active
---

# {name}

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
```

---

### subscription

Creates a recurring service record.

**Additional prompts:** Subscription name

```markdown
---
title: "{name}"
type: object
object_type: subscription
status: active
---

# {name}

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
```

---

### device

Creates a hardware/equipment record.

**Additional prompts:** Device name/description

```markdown
---
title: "{name}"
type: object
object_type: device
status: active
---

# {name}

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
```

---

### location

Creates a place record.

**Additional prompts:** Location name

```markdown
---
title: "{name}"
type: object
object_type: location
status: active
---

# {name}

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
```

---

*Back to [Templates Index](index.md) | See also: [object-financial](object-financial.md)*
