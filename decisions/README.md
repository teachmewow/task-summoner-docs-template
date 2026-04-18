# Decisions (ADRs)

Architecture Decision Records. One decision per file, append-only.

## Convention

- **Filename:** `YYYY-MM-<short-slug>.md` (e.g. `2026-04-architecture-dsl.md`).
- **Short-form ADR:** keep each file focused on one question. If the scope
  grows, split into multiple ADRs and cross-link them.
- **Append-only.** Never rewrite history — if a decision is reversed, add
  a new ADR that supersedes the old one and flip the old one's `status`
  to `superseded`.

## Frontmatter

Every ADR starts with YAML frontmatter the harness can index:

```yaml
---
date: 2026-04-18
status: accepted   # proposed | accepted | superseded | deprecated
tags: [architecture, tooling]
summary: One-sentence TL;DR of the decision.
---
```

After the frontmatter, use a short body with these headings:

- **Context** — what problem are we solving, what constraints apply.
- **Options** — alternatives considered, with pros/cons.
- **Decision** — what we chose and why.
- **Consequences** — what this unlocks, what it costs, what it rules out.

## Why append-only

This folder is the long-term memory the task-summoner agent reads before
planning new work. Rewriting history would let the agent silently drift
from the original rationale. New context gets a new ADR; the old one
stays as-is with an updated `status`.
