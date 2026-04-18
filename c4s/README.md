# C4 models

Master Structurizr DSL models per system, plus rendered diagrams.

## Convention

```
c4s/
  <system-name>.dsl           # top-level workspace (System Context + Containers)
  modules/
    <module-name>/
      components.dsl          # Level 3 (Components) for that container
      db-schema.md            # optional: DB schema narrative
```

- **One `.dsl` per system** at the top level. It defines the System Context
  (Level 1) and Container (Level 2) views.
- **Level 3 (Components)** lives under `modules/<module-name>/components.dsl`,
  one folder per container.
- **Rendered PNGs** go alongside the `.dsl` file that produced them, so a
  reviewer browsing GitHub can see the diagram without running Docker.

## Rendering

Render commands live in [`../.task-summoner/config.yml`](../.task-summoner/config.yml).
The default stack is **Structurizr → C4-PlantUML → PNG**, all via Docker —
no local toolchain install required.

```bash
# Export DSL to C4-PlantUML
docker run --rm -v "$PWD":/work -w /work structurizr/structurizr:latest \
  export -w c4s/<system>.dsl -f plantuml/c4plantuml -o c4s/

# Rasterise PUML to PNG
docker run --rm -v "$PWD/c4s":/work -w /work plantuml/plantuml:latest \
  -tpng 'structurizr-*.puml'
```

## Why master models (not per-RFC forks)

C4 models here are the **source of truth** for the architecture. RFCs
under [`../rfcs/`](../rfcs/) can include an `impact.dsl` that shows a
proposed change, but the master model in this folder is only updated
once the RFC is accepted and implemented.
