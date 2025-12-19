# object-financial Template

Create a financial object (account, credit card, loan, etc.)

---

## Prompts

| Prompt | Purpose | Example |
|--------|---------|---------|
| Account/Card Name | Identifier | "Chase Sapphire" |
| Financial type | account, credit-card, loan, utility, insurance, subscription | |
| Provider/Company | Institution | "Chase" |
| Account Number | Masked format | "****1234" |
| Status | active, inactive, closed | |
| Payment method | manual, auto-pay, direct-debit | |
| Amount | Payment amount | "55", "variable" |
| Due date | When payment due | "15", "25th" |
| Billing cycle | monthly, quarterly, annually, variable | |
| Phone | Customer service | |
| Website | Portal URL | |

**Credit card specific:**
- Credit limit
- Interest rate (APR)
- Rewards program

**Loan specific:**
- Original loan amount
- Loan term

---

## Output

**Filename:** `{financial_type}-{slug}.md`

**Location:** `Objects/` or track `resources/`

---

## Generated File

```markdown
---
title: "{name}"
type: object
object_type: financial
financial_type: {type}
status: {status}
created: YYYY-MM-DD
lastmod: YYYY-MM-DD
tags:
  - financial
  - {type}
provider: "{provider}"
account_number: "{masked}"
payment_method: {method}
amount: "{amount}"
due_date: "{due}"
billing_cycle: {cycle}
phone: "{phone}"
website: "{website}"
credit_limit: "{limit}"        # if credit card
interest_rate: "{rate}"        # if credit card
rewards_program: "{rewards}"   # if credit card
loan_amount: "{amount}"        # if loan
loan_term: "{term}"            # if loan
---

# {name}

## Overview

| Field | Value |
|-------|-------|
| Provider | {provider} |
| Type | {type} |
| Status | {status} |
| Account | {masked} |

## Payment Details

| Field | Value |
|-------|-------|
| Amount | {amount} |
| Due | {due} |
| Method | {method} |
| Cycle | {cycle} |

## Card Details (if credit card)

| Field | Value |
|-------|-------|
| Credit Limit | {limit} |
| Interest Rate | {rate} |
| Rewards | {rewards} |

## Loan Details (if loan)

| Field | Value |
|-------|-------|
| Original Amount | {amount} |
| Term | {term} |

## Contact

| Field | Value |
|-------|-------|
| Phone | {phone} |
| Website | {website} |

## Notes

---

## Log

---

YYYY-MM-DD - Created object


```

---

*Back to [Templates Index](index.md) | See also: [object](object.md)*
