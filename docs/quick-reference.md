# Quick Reference

A condensed reference for use during event modeling workshops.

## The 7 Steps

| Step | Name | Elements Added | Purpose |
|------|------|----------------|---------|
| 1 | Brainstorming | Events | Identify what happens |
| 2 | The Plot | Events (ordered) | Timeline of facts |
| 3 | The Storyboard | Wireframes | User interactions |
| 4 | Identify Inputs | Commands | User intents and actions |
| 5 | Identify Outputs | Views | Read models and queries |
| 6 | Apply Scenarios | Swim lanes | Organize by actor/aggregate |
| 7 | Elaborate (Slicing) | **Policies + Fields** | Implementation details |

## Base Model vs. Sliced Model

### Base Model (Steps 1–6)
**Purpose:** Understand the business process  
**Audience:** All stakeholders (business + technical)  
**Elements:** Events, commands, views, wireframes, swim lanes  
**Do not add:** Policies, fields

### Sliced Model (Step 7)
**Purpose:** Implementation planning  
**Audience:** Development team + domain experts  
**Elements:** Everything from base model + policies + fields  
**Add now:** Automation policies, business rules, data structures

## When to Use Each Element

### Always (Steps 1–6)
- **Events** — Business facts (past tense: "Order Placed", "Payment Confirmed")
- **Commands** — User intents (imperative: "Place Order", "Confirm Payment")
- **Views** — Read models (nouns: "Order List", "Payment History")
- **Wireframes** — User interfaces
- **Swim lanes** — Actor and aggregate organization

### Slicing Only (Step 7)
- **Policies** — Automation (reacts to events, triggers commands)
- **Fields** — Data structures (command params, event data, view columns)

## Common Mistakes

**Adding policies too early**  
Show manual workflows first. Stakeholders need to understand what happens before discussing how it is automated.

**Adding fields too early**  
Focus on process flow first. Detailed data structures overwhelm stakeholders and obscure the business process.

**Skipping the base model**  
Always build shared understanding before adding implementation detail.

## Quick Syntax

### Base Model

```plantuml
$wireframe(Customer Portal, Customer)           ' UI element
$command(Subscribe to Plan)                     ' User intent
$event(Subscription Activated, Subscription)    ' Business fact
$view(Active Subscriptions)                     ' Read model
```

### Slicing (Step 7 Only)

```plantuml
' Automation policy
$policy(Billing Cycle Policy)
$eventarrow(Subscription Activated, Billing Cycle Policy)
$command(Generate Invoice)
$commandarrow(Billing Cycle Policy, Generate Invoice)

' Business rules on policy
$policy(Billing Cycle Policy, $fields = "Check billing date\nGather charges\nCalculate total")

' Data structures
$command(Subscribe to Plan, $fields = "customerId: UUID\nplanId: UUID")
$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\nactivatedAt: timestamp")
$view(Active Subscriptions, $fields = "subscriptionId\nplanName\nstatus")
```

## Workshop Flow

```
Session 1: Base Model
├─ Steps 1–2: Brainstorm and order events
├─ Step 3: Add wireframes (who interacts)
├─ Step 4: Add commands (what actions)
├─ Step 5: Add views (what data is shown)
└─ Step 6: Organize into swim lanes
    └─ Output: Shared business understanding

Session 2: Slicing
├─ Step 7a: Identify automation (add policies)
├─ Step 7b: Define business rules (add policy fields)
├─ Step 7c: Specify data (add fields to commands/events/views)
└─ Step 7d: Prioritize implementation slices
    └─ Output: Technical specification
```

## Element Colors

| Color | Element |
|-------|---------|
| Orange | Events |
| Blue | Commands |
| Green | Views |
| White | Wireframes |
| Purple | Policies (slicing only) |
| Pink | External systems |
| Red | Questions |

## Further Reading

- [Getting Started](getting-started.md) — Installation and first diagram
- [Process Guide](process-guide.md) — Detailed walkthrough with examples
- [API Reference](api-reference.md) — All macros and parameters
- [EventModeling.org](https://eventmodeling.org/) — Official methodology

---

> Start simple. Understand the business process first. Add automation and technical details during slicing.
