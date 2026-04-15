# Fintech Billing System — Base Event Model

A **base event model** (Steps 1–6) demonstrating a comprehensive billing system for a fintech platform.

## Purpose

This diagram represents the **understanding phase** of event modeling:
- **Audience:** All stakeholders (business + technical)
- **Goal:** Understand the complete business process
- **Status:** Ready for stakeholder review and discussion

## Philosophy: Base Model First

This diagram follows event modeling best practices:
- **Start simple** — Shows only the core business flow without implementation details
- **Human first** — All processes shown as manual human actions (Finance team generates invoices, confirms payments, etc.)
- **No policies** — Automation patterns are identified later during slicing (Step 7)
- **No field details** — Data structures are defined when implementing slices (Step 7)

We want stakeholders to understand **what happens** before discussing **how it is automated**.

## Overview

The model covers:
- **Subscription Management** — Recurring billing cycles and plan changes
- **Line Item Ingestion** — Receiving chargeable items from external order systems
- **Manual Charges** — One-time charges added by billing administrators
- **Invoice Generation** — Manual invoice creation workflow
- **Invoice Approval & Delivery** — Finance team approval and delivery workflow
- **Payment Processing** — Customer payment submission and manual verification
- **Payment Application** — Manual matching of payments to invoices
- **Reconciliation** — Period-based reconciliation of invoices vs payments

## Business Workflows

The model shows 12 key business workflows in left-to-right order:

1. Subscribe to Plan
2. Change Subscription
3. Line Items from External System
4. Manual Charge Management
5. Invoice Generation
6. Invoice Approval
7. Invoice Delivery
8. Payment Processing
9. Payment Confirmation
10. Apply Payment
11. Reconciliation
12. Subscription Cancellation

## Actors and Aggregates

**Wireframe lanes (actors):**
- Customer — subscribes to plans, makes changes, submits payments
- BillingAdmin — adds manual charges, reviews line items
- FinanceTeam — generates/approves invoices, confirms payments, reconciles

**Event lanes (aggregates)** — all within the "Billing" bounded context:
- Subscription, LineItem, Invoice, Payment, Reconciliation

## Key Patterns

**External system integration:** Shows an external Order System delivering `Chargeable Item Received` events.

**Manual-first:** All workflows are human-driven. During slicing, teams will identify which should be automated (add policies), define business rules (add policy fields), and specify data structures (add element fields).

**Read models (views):** Active Subscriptions, Pending Charges, Invoices to Review, Approved Invoices, Payments Pending Verification, Confirmed Payments, Active Reconciliations, and more.

## Rendering

```bash
java -DPLANTUML_LIMIT_SIZE=8192 -jar plantuml.jar examples/fintech-billing-system.puml
```

See [Rendering Configuration](../docs/plantuml-png-rendering.md) for VS Code, CI, and other environments.

## Next Steps: Slicing (Step 7)

After stakeholders approve this base flow, create a sliced model by:

**Step 7a — Identify automation:** Add `$policy()` elements for workflows to automate:
- Billing Cycle Policy — automatically generate invoices on billing dates
- Payment Verification Policy — integrate with payment gateway
- Payment Application Policy — automatically apply payments to invoices
- Invoice Approval — keep as manual (Finance team review)

See [subscription-billing-with-policies.puml](subscription-billing-with-policies.puml) for an example.

**Step 7b — Define business rules:** Add `$fields` to policies:
```plantuml
$policy(Billing Cycle Policy, $fields = "Check billing cycle date\nGather pending charges\nCalculate proration\nValidate minimum amount")
```

See [subscription-billing-with-rules.puml](subscription-billing-with-rules.puml) for an example.

**Step 7c — Specify data structures:** Add `$fields` to commands, events, and views:
```plantuml
$command(Subscribe to Plan, $fields = "customerId: UUID\nplanId: UUID\nbillingCycle: monthly|annual")
$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\nactivatedAt: timestamp\nstatus: active")
$view(Active Subscriptions, $fields = "subscriptionId\nplanName\namount\nnextBillingDate")
```

See [subscription-billing-sliced.puml](subscription-billing-sliced.puml) for an example.

**Step 7d — Prioritize implementation slices:**

| Priority | Slice |
|----------|-------|
| P0 | Subscribe to Plan |
| P0 | Generate Invoice |
| P0 | Submit Payment |
| P1 | Change Subscription |
| P2 | Reconciliation |

## Notes

**Element names cannot contain hyphens.** Use spaces or camelCase:
- `Manual Charge` or `ManualCharge` — correct
- `Manual-Charge` — causes rendering errors

## Further Reading

- [Getting Started](../docs/getting-started.md)
- [Process Guide](../docs/process-guide.md)
- [API Reference](../docs/api-reference.md)
