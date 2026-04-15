# API Reference

All macros provided by `event-modeling-lib.iuml`. Parameters shown with `= ""` are optional and can be addressed by name: `$event(OrderPlaced, Order, $alias = "op")`.

## Configuration

### `$enableAutoAlias()`

Automatically generates PlantUML aliases for elements with duplicate names. `Order` becomes `Order`, `Order1`, `Order2`, etc. Must be called explicitly so the diagram author is aware that aliases are being generated.

### `$enableAutoSpacing()`

Automatically positions elements horizontally based on their definition order. Positioning is estimated from text width and is not pixel-precise due to PlantUML limitations. Adjust individual elements with the `$offset` parameter.

## Lane Configuration

### `$configureWireframeLane($laneAlias, $headingName = "", $headingAlias = "")`

Defines a wireframe lane representing an actor (user or external system). The actor figure at the start of the lane is labeled with `$headingName`, which defaults to `$laneAlias`.

### `$configureCommandViewLane($laneAlias, $headingName = "", $headingAlias = "")`

Defines a command/view lane without an actor figure at its head.

### `$configureEventLane($laneAlias, $headingName = "", $headingAlias = "", $context = "")`

Defines an aggregate lane. Use `$context` to group multiple event lanes into a shared bounded context frame.

## Elements

All element macros share these common optional parameters:

| Parameter | Description |
|-----------|-------------|
| `$offset` | Adjust horizontal positioning (not pixel-precise) |
| `$arrow` | `1` (default) auto-generates an arrow from the previous element; `0` suppresses it. Illogical arrows (e.g. event → event) are never generated regardless of this setting. |
| `$alias` | Manual PlantUML alias. Useful when renaming an element without updating all arrow references. |
| `$figure` | Override the visual shape. Any figure from the [deployment diagram](https://plantuml.com/deployment-diagram) is valid. |
| `$fields` | Data to display below the element name, lines separated by `\n`. A separator line is added automatically. See [Fields](#fields). |

### `$wireframe($name, $laneAlias = "defaultLane", $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

A UI screen or interface element. Placed in the wireframe lane identified by `$laneAlias`.

### `$command($name, $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

A user intent or action. Styled blue.

### `$event($name, $laneAlias = "defaultLane", $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

A business fact that has occurred. Placed in the event lane identified by `$laneAlias`. Styled orange.

### `$view($name, $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

A read model or query result. Styled green.

### `$policy($name, $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

An automation policy that reacts to events and triggers commands. **Add during slicing (Step 7) only.** Styled purple.

### `$extra($name, $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

A generic element placed in the top lane. Useful for translation layers, sagas, and automation not easily categorized as a policy. Styled light grey.

### `$externalsystem($name, $laneAlias = "defaultLane", $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

An external system integration. Styled pink.

### `$question($name, $laneAlias = "defaultLane", $offset = 0, $arrow = 1, $alias = "", $figure = $default_figure, $fields = "")`

An open question or unresolved decision to capture during a workshop. Styled red.

### `$timeline($name, $offset = 0)`

A timeline marker. Styled as a solid black bar.

### Persona Variants

Each element type has a persona variant that renders with a person figure:

- `$wireframe_persona()`, `$command_persona()`, `$event_persona()`, `$view_persona()`

Parameters are identical to the base variant.

## Arrows

Arrows connect elements by their aliases. When `$enableAutoAlias()` is active and no manual `$alias` is set, the alias is the element name (with a numeric suffix for duplicates).

### `$arrow($from, $to)`

A default black arrow between two elements.

### `$commandarrow($from, $to)`

An arrow styled in the command color (blue).

### `$eventarrow($from, $to)`

An arrow styled in the event color (orange).

### `$externalarrow($from, $to)`

An arrow styled in the external system color (pink).

### `$policyarrow($from, $to)`

An arrow styled in the policy color (purple).

## Rendering

### `$renderEventModelingDiagram()`

Emits all recorded elements to PlantUML. Must be the last call in the diagram. All element macros before this call record data into an internal structure; this call is what actually produces output.

---

## Fields

The `$fields` parameter adds structured data below any element's name. It is intended for the **slicing phase (Step 7)** — avoid adding fields to a base model.

### Syntax

```plantuml
$command(Name, $fields = "field1: type\nfield2: type")
$event(Name, Lane, $fields = "field1: type\nfield2: type")
$view(Name, $fields = "field1\nfield2\nfield3")
$policy(Name, $fields = "Rule 1\nRule 2")
$wireframe(Name, Lane, $fields = "Element 1\nElement 2")
```

Each `\n` produces a new line. A separator line is automatically inserted between the element name and its fields.

### Formatting Options

**Plain list:**
```plantuml
$fields = "field1\nfield2\nfield3"
```

**Typed fields:**
```plantuml
$fields = "customerId: UUID\nplanId: UUID\nstartDate: date"
```

**Bold section headers** (using [PlantUML creole](https://plantuml.com/creole)):
```plantuml
$fields = "<b>Required:</b>\ncustomerId: UUID\n<b>Optional:</b>\ndiscountCode: string"
```

**Bullet points:**
```plantuml
$fields = "• Check stock levels\n• Reserve items\n• Notify if unavailable"
```

**Mixed format:**
```plantuml
$fields = "<b>Input:</b>\n• userId: UUID\n• amount: decimal\n<b>Validation:</b>\n✓ Amount > 0\n✓ User exists"
```

### Common Patterns by Element Type

**Command** — input parameters:
```plantuml
$command(Place Order, $fields = "customerId: UUID\nitems: OrderItem[]\nshippingAddress: Address")
```

**Event** — domain data:
```plantuml
$event(Order Placed, Order, $fields = "orderId: UUID\ncustomerId: UUID\ntotalAmount: decimal\nplacedAt: timestamp")
```

**View** — display fields:
```plantuml
$view(Order History, $fields = "orderId\ndate\nitems\ntotal\nstatus")
```

**Policy** — business rules:
```plantuml
$policy(Fraud Detection Policy, $fields = "Check velocity limits\nValidate billing address\nScore transaction risk")
```

See [examples/fields-example.puml](../examples/fields-example.puml) for a comprehensive demonstration across all element types.

---

## Customization

All styles, variables, and procedures in the library can be overridden by redefining them after the include.

### Styles

Override element colors using PlantUML `<style>` blocks:

```plantuml
@startuml
!include_once event-modeling-lib.iuml

<style>
    .event {
        BackgroundColor #FFD700
    }
    .command {
        BackgroundColor #ADD8E6
    }
</style>

' elements here

$renderEventModelingDiagram()
@enduml
```

Available style classes and their defaults:

| Class | Default Color | Element |
|-------|--------------|---------|
| `.command` | Light blue | Commands |
| `.event` | Orange | Events |
| `.view` | Pale green | Views |
| `.wireframe` | White | Wireframes |
| `.policy` | Light purple | Policies |
| `.externalsystem` | Pink | External systems |
| `.question` | Red | Questions |
| `.extra` | Light grey | Extra elements |
| `.timeline` | Black | Timeline markers |

See [PlantUML styling options](https://plantuml.com/style-evolution) for available properties. Note that PlantUML styling is not fully documented and not all properties work in all contexts.

### Variables

Override global defaults after the include:

```plantuml
!include_once event-modeling-lib.iuml
!$default_figure = "rectangle"
```

### Text and Image Formatting

Element names support [PlantUML creole syntax](https://plantuml.com/creole) for rich text and embedded images:

```plantuml
$event(OrderSubmitted\n---\n<b>Example comment, $alias = "OrderSubmitted")
$wireframe(<img:http://plantuml.com/logo3.png>, $alias = "imageElement")
```

![Text and image formatting example](../examples/Example2.png)
