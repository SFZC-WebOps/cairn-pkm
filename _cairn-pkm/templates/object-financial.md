<%*
// Prompt for basic info
const title = await tp.system.prompt("Account/Card Name");
if (!title) {
    new Notice("No name provided - canceling");
    return;
}

const financialType = await tp.system.suggester(
    ["account", "credit-card", "loan", "utility", "insurance", "subscription"],
    ["account", "credit-card", "loan", "utility", "insurance", "subscription"]
);

const provider = await tp.system.prompt("Provider/Company");
const accountNumber = await tp.system.prompt("Account Number (masked like ****1234)", "", true);
const status = await tp.system.suggester(
    ["active", "inactive", "closed"],
    ["active", "inactive", "closed"]
) || "active";

// Payment info
const paymentMethod = await tp.system.suggester(
    ["manual", "auto-pay", "direct-debit"],
    ["manual", "auto-pay", "direct-debit"]
) || "manual";

const amount = await tp.system.prompt("Payment Amount (e.g., 55, variable, ~100)", "", true);
const dueDate = await tp.system.prompt("Due Date (e.g., 15, 25th, variable)", "", true);
const billingCycle = await tp.system.suggester(
    ["monthly", "quarterly", "annually", "variable"],
    ["monthly", "quarterly", "annually", "variable"]
) || "monthly";

// Contact info
const phone = await tp.system.prompt("Customer Service Phone", "", true);
const website = await tp.system.prompt("Website", "", true);

// Credit card specific
let creditLimit = "";
let interestRate = "";
let rewardsProgram = "";
if (financialType === "credit-card") {
    creditLimit = await tp.system.prompt("Credit Limit", "", true);
    interestRate = await tp.system.prompt("Interest Rate (APR)", "", true);
    rewardsProgram = await tp.system.prompt("Rewards Program", "", true);
}

// Loan specific
let loanAmount = "";
let loanTerm = "";
if (financialType === "loan") {
    loanAmount = await tp.system.prompt("Original Loan Amount", "", true);
    loanTerm = await tp.system.prompt("Loan Term (e.g., 60 months)", "", true);
}

// Generate filename
const slug = title.toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .replace(/-+/g, '-')
    .replace(/^-|-$/g, '');
const filename = `${financialType}-${slug}`;
await tp.file.rename(filename);

const today = tp.date.now("YYYY-MM-DD");
-%>
---
title: "<% title %>"
type: object
object_type: financial
financial_type: <% financialType %>
status: <% status %>
created: <% today %>
lastmod: <% today %>
tags:
  - financial
  - <% financialType %>
provider: "<% provider %>"
account_number: "<% accountNumber %>"
payment_method: <% paymentMethod %>
amount: "<% amount %>"
due_date: "<% dueDate %>"
billing_cycle: <% billingCycle %>
phone: "<% phone %>"
website: "<% website %>"
<%* if (creditLimit) { -%>
credit_limit: "<% creditLimit %>"
<%* } -%>
<%* if (interestRate) { -%>
interest_rate: "<% interestRate %>"
<%* } -%>
<%* if (rewardsProgram) { -%>
rewards_program: "<% rewardsProgram %>"
<%* } -%>
<%* if (loanAmount) { -%>
loan_amount: "<% loanAmount %>"
<%* } -%>
<%* if (loanTerm) { -%>
loan_term: "<% loanTerm %>"
<%* } -%>
---

# <% title %>

## Overview

| Field | Value |
|-------|-------|
| Provider | <% provider %> |
| Type | <% financialType %> |
| Status | <% status %> |
| Account | <% accountNumber %> |

## Payment Details

| Field | Value |
|-------|-------|
| Amount | <% amount %> |
| Due | <% dueDate %> |
| Method | <% paymentMethod %> |
| Cycle | <% billingCycle %> |

<%* if (financialType === "credit-card") { -%>
## Card Details

| Field | Value |
|-------|-------|
| Credit Limit | <% creditLimit %> |
| Interest Rate | <% interestRate %> |
| Rewards | <% rewardsProgram %> |

<%* } -%>
<%* if (financialType === "loan") { -%>
## Loan Details

| Field | Value |
|-------|-------|
| Original Amount | <% loanAmount %> |
| Term | <% loanTerm %> |

<%* } -%>
## Contact

| Field | Value |
|-------|-------|
| Phone | <% phone %> |
| Website | <% website %> |

## Notes

---

## Log

---

<% today %> - Created object

