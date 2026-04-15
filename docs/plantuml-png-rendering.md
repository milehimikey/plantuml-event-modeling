# PlantUML PNG Rendering

Event model diagrams in this repo are wide by nature. PlantUML's default PNG output limit is **4096px**, which cuts off large diagrams. The limit is raised to **8192px** via the JVM flag `-DPLANTUML_LIMIT_SIZE=8192`.

## VS Code

Handled automatically via `.vscode/settings.json` — no manual steps required.

## Other rendering contexts

| Tool | How to apply the flag |
|------|-----------------------|
| **CLI** | `java -DPLANTUML_LIMIT_SIZE=8192 -jar plantuml.jar yourfile.puml` |
| **Homebrew `plantuml` wrapper** | `_JAVA_OPTIONS="-DPLANTUML_LIMIT_SIZE=8192" plantuml yourfile.puml` |
| **CI/CD (GitHub Actions, etc.)** | Add `-DPLANTUML_LIMIT_SIZE=8192` to your JVM or plantuml invocation step |
| **IntelliJ PlantUML plugin** | Settings → PlantUML → VM Options: `-DPLANTUML_LIMIT_SIZE=8192` |

## Increasing the limit further

If a diagram grows beyond 8192px, increase the value in `.vscode/settings.json` and update the table above:

```json
{
  "plantuml.jarArgs": ["-DPLANTUML_LIMIT_SIZE=16384"]
}
```
