# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Project Is

A PlantUML preprocessor macro library (`event-modeling-lib.iuml`) for creating [Event Modeling](https://eventmodeling.org/) diagrams. There is no build system, no package manager, and no test runner — all logic is PlantUML preprocessor code.

## Rendering Diagrams

To render `.puml` files, use the PlantUML CLI or any PlantUML-compatible tool (VS Code extension, IntelliJ plugin, web editor). For wide diagrams, pass the JVM flag:

```
java -DPLANTUML_LIMIT_SIZE=8192 -jar plantuml.jar <file.puml>
```

See `docs/plantuml-png-rendering.md` for configuration details.

The library can be included locally or remotely:
```plantuml
!include_once event-modeling-lib.iuml
' or
!include_once https://raw.githubusercontent.com/chilit-nl/plantuml-event-modeling/main/event-modeling-lib.iuml
```

## Architecture of `event-modeling-lib.iuml`

The library works in two phases: **recording** (user calls macros that store data in pseudo-variables) and **rendering** (a single `$renderEventModelingDiagram()` call outputs all PlantUML elements in the correct order).

### Pseudo-Data Structures
PlantUML has no real data structures, so the library simulates them with named variables using a naming convention:
- `$event-{laneIndex}-items-{itemIndex}-name` — element name in a lane
- `$lane-{index}-type` — lane type
- `$arrow-{index}-from` / `$arrow-{index}-to` — stored arrows

`$_get()`, `$_set()`, `$_exists()` are the accessor helpers.

### Lane Types (11 total)
`wireframe`, `commandview`, `event`, `timeline`, `extra`, `externalsystem`, `question`, `policy`, and persona variants (`event_persona`, `commandview_persona`, `wireframe_persona`).

### Public API (bottom ~150 lines of the file)

| Category | Macros |
|----------|--------|
| Configuration | `$enableAutoAlias()`, `$enableAutoSpacing()`, `$configureLane*()` |
| Actor lanes | `$configureWireframeLane(Name)`, `$configureCommandViewLane(Name)` |
| Aggregate lanes | `$configureEventLane(Name, $context=X)` |
| Elements | `$wireframe()`, `$command()`, `$event()`, `$view()`, `$policy()`, `$externalsystem()`, `$question()`, `$timeline()` |
| Arrows | `$arrow()`, `$commandarrow()`, `$eventarrow()`, `$externalarrow()`, `$policyarrow()` |
| Render | `$renderEventModelingDiagram()` |

All element macros accept an optional `$fields` parameter for structured data display (Step 7 slicing only — not for base models).

### Style System
Colors are defined via PlantUML `<style>` blocks at the top of the library. Override after include:
- `.command` → light blue
- `.event` → orange  
- `.view` → pale green
- `.wireframe` → white
- `.policy` → light purple
- `.externalsystem` → pink
- `.question` → red

### Auto-Spacing Engine
When `$enableAutoSpacing()` is active, the library estimates text width from character count and tracks per-lane used widths to auto-position elements horizontally via hidden arrows. Override global spacers with `$autospacing_*` variables.

### Auto-Alias
When `$enableAutoAlias()` is active, element names become their PlantUML aliases automatically. Duplicates get numeric suffixes (`Name`, `Name1`, `Name2`).

## Key Design Principles

1. **Two-phase rendering**: all element calls are recorded first; `$renderEventModelingDiagram()` emits all PlantUML output at the end.
2. **GraphViz only**: the structural hidden-arrow technique only works with the default GraphViz layout engine.
3. **Progressive complexity**: base model (Steps 1–6) uses only `$wireframe`, `$command`, `$event`, `$view`. Policies and `$fields` are Step 7 (slicing) features.

## Repository Structure

```
event-modeling-lib.iuml        # The entire library — single file
README.md                      # Main entry point
docs/
  getting-started.md           # Installation, first diagram, naming conventions
  process-guide.md             # 7-step methodology, phases, best practices
  api-reference.md             # All macros, fields parameter, customization
  quick-reference.md           # One-page workshop cheat sheet
  plantuml-png-rendering.md    # Wide diagram rendering configuration
examples/                      # .puml source files and rendered .png output
  subscription-billing-*.puml  # Four-phase progressive workflow example
  fintech-billing-system.puml  # Comprehensive real-world base model
```

## Git Remotes

- `origin` — `git@github.com:milehimikey/plantuml-event-modeling.git` (this fork)
- `upstream` — `git@github.com:chilit-nl/plantuml-event-modeling.git` (official repo)
