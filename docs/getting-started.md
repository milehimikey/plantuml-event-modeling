# Getting Started

## Installation

Include the library at the top of any `.puml` file. You can reference the hosted version or a local copy:

```plantuml
' Remote (always latest)
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

' Local copy
!include_once path/to/event-modeling-lib.iuml
```

For wide diagrams, PlantUML's default PNG size limit needs to be raised. See [Rendering Configuration](plantuml-png-rendering.md).

## Understand the Methodology First

This library implements the [Event Modeling](https://eventmodeling.org/) methodology, which works in two distinct phases:

- **Base Model (Steps 1–6):** Understand the business process. Use only events, commands, views, wireframes, and swim lanes.
- **Sliced Model (Step 7):** Plan implementation. Add policies and field definitions.

The single most important rule: **do not add `$policy()` or `$fields` in your base model.** These belong exclusively in Step 7.

See the [Process Guide](process-guide.md) for a full walkthrough of all seven steps.

## Your First Diagram

### 1. Basic structure

Every diagram uses the same skeleton:

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

' Configure lanes and add elements here

$renderEventModelingDiagram()
@enduml
```

`$renderEventModelingDiagram()` must be the last call. All element macros before it record data; this call emits the diagram.

### 2. Configure lanes

Lanes define the horizontal swim lanes of the diagram.

```plantuml
' Wireframe lanes represent actors (users, external systems)
$configureWireframeLane(Customer)
$configureWireframeLane(Admin)

' Event lanes represent aggregates, optionally grouped into bounded contexts
$configureEventLane(Order, $context = "Sales")
$configureEventLane(Payment, $context = "Billing")
```

### 3. Add elements in workflow order

Add elements left-to-right in the order they occur in the business process:

```plantuml
$wireframe(Shopping Cart, Customer)    ' UI screen
$command(Place Order)                  ' User action (imperative)
$event(Order Placed, Order)            ' Business fact (past tense)
$view(Order History)                   ' Read model
$wireframe(Customer Portal, Customer)
$arrow(Order History, Customer Portal) ' Explicit data flow
```

### 4. Complete base model

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

$configureWireframeLane(Customer)
$configureEventLane(Order)

$wireframe(Shopping Cart, Customer)
$command(Place Order)
$event(Order Placed, Order)
$view(Order History)
$wireframe(Customer Portal, Customer)
$arrow(Order History, Customer Portal)

$renderEventModelingDiagram()
@enduml
```

Stop here for an initial workshop session. Share the diagram with stakeholders, discuss, and iterate before adding any implementation detail.

## Naming Conventions

**Element names cannot contain hyphens.** Use spaces instead:

```plantuml
$event(Order Placed)   ' correct
$event(Order-Placed)   ' causes rendering errors
```

**Events use past tense** — they are facts that have already occurred:

```plantuml
$event(Order Placed)       ' correct
$event(Payment Received)   ' correct
$event(Place Order)        ' incorrect — imperative
```

**Commands use imperative mood** — they represent user intent:

```plantuml
$command(Place Order)      ' correct
$command(Submit Payment)   ' correct
$command(Order Placed)     ' incorrect — past tense
```

## Next Steps

- [Process Guide](process-guide.md) — The full 7-step methodology with phase-by-phase examples
- [API Reference](api-reference.md) — All macros, parameters, and customization options
- [Quick Reference](quick-reference.md) — One-page cheat sheet for workshops
- [Progressive Examples](../examples/) — A system evolving from base model to implementation specification
