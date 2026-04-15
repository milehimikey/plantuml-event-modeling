# Event Modeling Process Guide

This guide walks through the complete event modeling process from initial brainstorming through slicing and implementation planning. Event modeling is a workshop technique that builds understanding progressively — each phase adds a layer of detail on top of what came before.

## The 7 Steps

As defined at [eventmodeling.org](https://eventmodeling.org/posts/what-is-event-modeling/):

| Step | Name | Elements Added | Purpose |
|------|------|----------------|---------|
| 1 | Brainstorming | Events | Identify what happens |
| 2 | The Plot | Events (ordered) | Timeline of facts |
| 3 | The Storyboard | Wireframes | User interactions |
| 4 | Identify Inputs | Commands | User intents and actions |
| 5 | Identify Outputs | Views | Read models and queries |
| 6 | Apply Scenarios | Swim lanes | Organize by actor and aggregate |
| 7 | Elaborate (Slicing) | Policies, Fields | Implementation details |

## Base Model vs. Sliced Model

The most important distinction in event modeling is between the base model and the sliced model.

### Base Model (Steps 1–6)

- **Purpose:** Understand the business process
- **Audience:** All stakeholders — business and technical
- **Elements:** Events, commands, views, wireframes, swim lanes
- **Do not include** policies or fields at this stage

### Sliced Model (Step 7)

- **Purpose:** Plan implementation
- **Audience:** Development team and domain experts
- **Elements:** Everything from the base model, plus policies and field definitions
- **This is where technical details emerge**

---

## Phase 1: Base Event Model (Steps 1–6)

**Goal:** Understand the complete business process without implementation details.

See [subscription-billing-base.puml](../examples/subscription-billing-base.puml) for a complete working example.

**Characteristics of a well-formed base model:**
- Shows the full business flow left to right
- All actions are human-driven (no automation assumed)
- Clear pattern: UI → Command → Event → View
- No policies
- No field definitions

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

$configureWireframeLane(Customer)
$configureEventLane(Subscription, $context = Billing)

$wireframe(Pricing Page, Customer)
$command(Subscribe to Plan)
$event(Subscription Activated, Subscription)
$view(Active Subscriptions)
$wireframe(Customer Portal, Customer)
$arrow(Active Subscriptions, Customer Portal)

$renderEventModelingDiagram()
@enduml
```

At this phase, "Generate Invoice" is a manual action by the Finance team. The focus is on **what happens**, not on how it is triggered or automated.

---

## Phase 2: Slicing — Add Automation (Step 7a)

**Goal:** Identify which workflows should be automated.

See [subscription-billing-with-policies.puml](../examples/subscription-billing-with-policies.puml) for a complete working example.

Policies react to events and trigger commands. Adding them identifies what should be automated vs. kept as a manual process.

```plantuml
' Policy that reacts to an event and triggers a command
$policy(Billing Cycle Policy)
$eventarrow(Subscription Activated, Billing Cycle Policy)
$command(Generate Invoice)
$commandarrow(Billing Cycle Policy, Generate Invoice)
```

---

## Phase 3: Slicing — Add Business Rules (Step 7b)

**Goal:** Define the business logic and invariants for each policy.

See [subscription-billing-with-rules.puml](../examples/subscription-billing-with-rules.puml) for a complete working example.

Add `$fields` to policies to describe their logic. This is where domain experts specify the actual business rules, which become acceptance criteria for implementation.

```plantuml
$policy(Billing Cycle Policy, $fields = "Check billing cycle date\nGather all pending charges\nCalculate proration if needed\nAggregate subscription fees\nValidate minimum charge amount")

$policy(Payment Verification Policy, $fields = "Call payment gateway API\nValidate funds availability\nProcess transaction\nHandle 3D Secure if required\nRetry on timeout (max 3 attempts)")
```

---

## Phase 4: Slicing — Add Data Structures (Step 7c)

**Goal:** Specify exact fields for commands, events, and views.

See [subscription-billing-sliced.puml](../examples/subscription-billing-sliced.puml) for a complete working example.

Adding `$fields` to commands, events, and views turns the diagram into an API contract. The development team now knows exactly what data to capture, store, and display.

```plantuml
$command(Subscribe to Plan, $fields = "customerId: UUID\nplanId: UUID\nbillingCycle: monthly|annual\nstartDate: date\npaymentMethodId: UUID")

$event(Subscription Activated, Subscription, $fields = "subscriptionId: UUID\ncustomerId: UUID\nplanId: UUID\nrecurringAmount: decimal\nbillingCycle: monthly|annual\nactivatedAt: timestamp\nstatus: active")

$view(Active Subscriptions, $fields = "subscriptionId\nplanName\ncustomerName\namount\nnextBillingDate\nstatus\ncreatedAt")
```

---

## Phase 5: Implementation Slicing

**Goal:** Prioritize vertical slices for iterative development.

Each vertical slice is a complete, independently deliverable workflow. Prioritize by business value:

| Priority | Slice | Description |
|----------|-------|-------------|
| P0 | Subscribe to Plan | Core value |
| P0 | Generate Invoice | Core value |
| P0 | Submit Payment | Core value |
| P1 | Change Subscription | Important |
| P1 | Payment Verification | Important |
| P2 | Reconciliation | Nice to have |

Each slice includes: wireframe, command handler, event, view projection, and end-to-end tests.

---

## Summary

```
Phase 1: Base Model (Steps 1–6)
├─ Events only → what happens
├─ + Wireframes → who interacts
├─ + Commands → what actions are taken
├─ + Views → what data is shown
└─ + Swim lanes → organize by actor/aggregate
    └─ Result: shared understanding of the business process

Phase 2: Slicing (Step 7)
├─ + Policies → what should be automated
├─ + Policy fields → how does automation work
└─ + Element fields → exact data structures
    └─ Result: implementation specification
```

## Best Practices

1. **Do not skip the base model** — Resist the urge to add policies or fields until the base model is complete and understood by all stakeholders
2. **Workshop format** — Do this collaboratively with both business and technical stakeholders
3. **Iterate** — Each phase adds detail; go back and refine as understanding deepens
4. **Focus on value** — Not every workflow needs automation; keep policies where they add real value
5. **Vertical slices** — Each slice should be independently deliverable from UI to event store
