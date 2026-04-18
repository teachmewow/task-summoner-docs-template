# task-summoner-docs-template

A GitHub **template repository** for SDLC documentation driven by the
[`task-summoner`](https://github.com/teachmewow) harness: per-issue RFCs,
append-only Architecture Decision Records (ADRs), and C4 architecture
diagrams written in Structurizr DSL.

This template gives you a docs repo that is **self-describing** (the
harness reads `.task-summoner/config.yml` to find everything) and
**reviewable on GitHub** (plain markdown + committed PNGs, reviewed via
pull requests).

## What this template is for

The `task-summoner` harness treats a docs repo as the long-term memory
of a codebase. Before planning new work, the agent reads the
`decisions/` folder; while implementing, it writes an RFC into `rfcs/`;
when the architecture shifts, it updates `c4s/`. For that to work, the
harness needs a predictable shape — that's what this template provides.

Use it whenever you start a new project that will be driven (even
partially) by the task-summoner harness.

## Create a repo from this template

**GitHub CLI:**

```bash
gh repo create my-docs --template teachmewow/task-summoner-docs-template --public
```

**GitHub UI:** click **"Use this template" → "Create a new repository"**
on the repo page.

Both produce a fresh repo with this structure and a single initial
commit (no history from the template).

## Repo structure

```
.task-summoner/
  config.yml                  # self-describing: paths + render commands
rfcs/
  README.md                   # per-issue RFCs at rfcs/<issue-id>/README.md
decisions/
  README.md                   # append-only ADRs with YAML frontmatter
c4s/
  README.md                   # master Structurizr models per system
.github/
  PULL_REQUEST_TEMPLATE.md    # Context / Decision / Evidence
  workflows/                  # render-diagrams.yml lands here (see ENG-97)
README.md
```

Each folder has a `README.md` that documents its own convention — read
those first before adding content.

## `.task-summoner/config.yml`

The harness discovers a docs repo by reading this file. Keeping it
in-repo means forks can diverge without patching the harness.

```yaml
docs_paths:
  rfcs: rfcs
  decisions: decisions
  c4s: c4s
render:
  dsl: structurizr
  export_command: "docker run --rm -v \"$PWD\":/work -w /work structurizr/structurizr:latest export -w {input} -f plantuml/c4plantuml -o {output_dir}"
  png_command: "docker run --rm -v \"$PWD/{output_dir}\":/work -w /work plantuml/plantuml:latest -tpng 'structurizr-*.puml'"
```

### Schema

| Key | Purpose |
|-----|---------|
| `docs_paths.rfcs` | Folder (relative to repo root) where per-issue RFCs live. |
| `docs_paths.decisions` | Folder for append-only ADRs. |
| `docs_paths.c4s` | Folder for master C4 models. |
| `render.dsl` | Architecture DSL in use. Currently only `structurizr`. |
| `render.export_command` | Shell command that turns a `.dsl` file into C4-PlantUML `.puml` files. `{input}` and `{output_dir}` are substituted by the harness. |
| `render.png_command` | Shell command that rasterises the `.puml` files into PNGs. `{output_dir}` is substituted. |

Override any path if your repo uses different folder names — the
harness will follow the config.

## RFC convention

Per-issue RFCs live at `rfcs/<issue-id>/README.md`. A folder lets each
RFC colocate its diagram:

```
rfcs/
  ENG-123/
    README.md      # Context / Options / Decision / Plan / Rollback
    impact.dsl     # optional: Structurizr DSL of the proposed change
    impact.png     # rendered PNG, committed alongside the DSL
```

Rendered PNGs are committed so reviewers without Docker can read the
diagram inline in the PR.

The skill that automates "generate an RFC from a Linear issue" is
tracked as **ENG-91** — once it lands, RFC creation becomes a single
slash command.

## ADR convention

Append-only, one decision per file, filename `YYYY-MM-<slug>.md`. Every
ADR starts with YAML frontmatter the harness indexes:

```yaml
---
date: 2026-04-18
status: accepted           # proposed | accepted | superseded | deprecated
tags: [architecture, tooling]
summary: One-sentence TL;DR of the decision.
---
```

Body uses: **Context**, **Options**, **Decision**, **Consequences**.
Never rewrite history — if a decision is reversed, add a new ADR that
supersedes the old one and flip the old one's `status` to `superseded`.

## Pointing task-summoner at a local fork

The task-summoner CLI accepts an absolute path to the docs repo, so you
can work on docs locally before pushing:

```bash
task-summoner config set docs_repo <absolute-path-to-your-fork>
```

> The CLI side of this is tracked as **ENG-94**. Until that ships, the
> harness falls back to the default docs repo configured at the org
> level.

## Why these conventions

The choices baked into this template come out of two decision records
on the TeachMeWoW docs repo:

- **Storage & review platform — git + markdown + GitHub PRs:**
  [ENG-82](https://linear.app/teachmewow/issue/ENG-82). TL;DR: markdown
  in a git repo reviewed via PRs is the lowest-friction path that still
  gives us history, diffing, and code-review muscle memory.
- **Architecture DSL — Structurizr → C4-PlantUML → PNG via Docker:**
  [ENG-85](https://linear.app/teachmewow/issue/ENG-85). TL;DR:
  Structurizr is the official C4 DSL; rendering through PlantUML keeps
  the toolchain Docker-only and deterministic.

## Long-term memory angle

The `decisions/` folder is not just documentation — it is the context
the task-summoner agent reads **before planning** any new work. That's
why ADRs are append-only and indexed by frontmatter: the agent needs a
stable, queryable record of why the system is the way it is, so it can
plan changes that respect prior decisions instead of silently
contradicting them.

Treat an ADR the way you'd treat a migration: once it's merged, it's
history. Amend by adding a new one.
