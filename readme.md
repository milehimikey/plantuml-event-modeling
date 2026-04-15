# Event Modeling Library for PlantUML

A library for fast creation of [Event Modeling](https://eventmodeling.org/) diagrams in PlantUML. The layout is generated automatically from the elements you define, enabling rapid iteration during modeling sessions.

> Only the default GraphViz layout engine is supported.

## Quick Example

![Example diagram](examples/Example1.png)

```plantuml
@startuml
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml

$enableAutoAlias()
$enableAutoSpacing()

$configureWireframeLane(Customer)
$configureWireframeLane(Employee)
$configureEventLane(Order, $context = Order)

$wireframe(OrderScreen, Customer)
$command(SubmitOrder)
$event(OrderSubmitted, Order)
$view(Orders)
$arrow(Orders, OrderScreen)

$wireframe(EmployeeScreen, Employee)
$command(DispatchOrder)
$event(OrderDispatched, Order)
$view(Orders)
$wireframe(OrderScreen, Customer)

$renderEventModelingDiagram()
@enduml
```

[Live edit this example](https://www.plantuml.com/plantuml/uml/XP912zim38Nl_XKU19P0qzm7WvQsjusDzR3ZS1mr4TXoiELs-_Tpd1fIMDbHxtkHdXGV0YlFqHhn0KcREC0lHnhan3o7JrtdrMC_8a-nZm6yTiH0lDVETdf2WzoIwMQZQ6dHmZt1rhe13DBOMONKlMgjmNwFopXH0QdUm36oEnfKeMwsqdLMElcJA3xAawCRZj63v0aaAGj1kbikqcW8ejB5yHCzt7noV4cWFiN0pe9ltZekTZRk3y3MSLaYP_hD3-1tSa4Clrb-bbd9eRvxTyS1f396WW4Bn44YIRSuHFKeOzOcL-q1Q2TV4bchQAYliRV8pyPLljVwpGeOXfqiz8xmM658bVBUbJ9xofn6R0EK7Kk6Kcyod37CYlNqZnG5lO_ntrNJjNu09PH_sO1gJ6vH-QpbQtzzle_pokf671ACrlm5)

## Documentation

| Guide | Description |
|-------|-------------|
| [Getting Started](docs/getting-started.md) | Installation, first diagram, naming conventions |
| [Process Guide](docs/process-guide.md) | The 7-step methodology, phases, and best practices |
| [API Reference](docs/api-reference.md) | All macros, parameters, fields, and customization |
| [Quick Reference](docs/quick-reference.md) | One-page cheat sheet for workshops |
| [Rendering](docs/plantuml-png-rendering.md) | Wide diagram configuration for VS Code and CI |

## Examples

### Progressive Workflow

The same subscription billing system shown through each phase of the methodology:

| Phase | File | Description |
|-------|------|-------------|
| Base Model (Steps 1–6) | [subscription-billing-base.puml](examples/subscription-billing-base.puml) | Business process only — no policies, no fields |
| Add Policies (Step 7a) | [subscription-billing-with-policies.puml](examples/subscription-billing-with-policies.puml) | Automation identified |
| Add Business Rules (Step 7b) | [subscription-billing-with-rules.puml](examples/subscription-billing-with-rules.puml) | Policy logic defined |
| Add Data Structures (Step 7c) | [subscription-billing-sliced.puml](examples/subscription-billing-sliced.puml) | Implementation-ready specification |

### Additional Examples

- [Iterative session example](examples/iterative-example.md) — Steps 1–6 in detail
- [Extensive example](examples/extensive-example.md) — Complex multi-aggregate scenario
- [Fintech billing system](examples/fintech-billing-system.puml) — Comprehensive real-world base model with external system integration

## Background

This library was developed for live creation of Event Modeling diagrams during workshop sessions. It is inspired by the [AxonIQ Event Modeler](https://morlack.github.io/eventmodeler/) stub, expanding on it with wireframe lanes, bounded contexts, and greater flexibility.

Building on PlantUML provides wide [compatibility](https://plantuml.com/running) and many export options. The layout and styling options are intentionally limited by how PlantUML's GraphViz engine works — if you need precise manual element positioning, this library is not the right fit.

## Wishlist

- IDE plugins for syntax highlighting and autocompletion tailored to this library
