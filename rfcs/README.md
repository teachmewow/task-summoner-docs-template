# RFCs

This folder holds per-issue RFCs at `rfcs/<issue-id>/README.md`.

## Convention

```
rfcs/
  <issue-id>/
    README.md     # the RFC itself (context, options, decision, plan)
    impact.dsl    # optional: Structurizr DSL showing the change on the C4 model
    impact.png    # optional: rendered diagram, committed alongside the DSL
```

- **Issue ID** is the tracker identifier the RFC maps to (e.g. `ENG-91`).
  Prefix with a date only if your tracker IDs are not globally unique.
- **One RFC per issue.** If the scope grows, split into new issues rather
  than stuffing a second RFC into the same folder.
- **`impact.dsl`** is optional but strongly encouraged for any change that
  crosses a container boundary. Render it via the command in
  `.task-summoner/config.yml` and commit the resulting PNG so reviewers
  without Docker can still read the diagram.
- **RFC README.md** is short-form: Context, Options considered, Decision,
  Implementation plan, Rollback. Link out to ADRs for decisions that
  outlive the RFC.

## Why this shape

RFCs are ephemeral — they answer "what are we going to do for this
ticket?". Decisions that outlive a single ticket belong in
[`../decisions/`](../decisions/), which is append-only.

## Automation

The `task-summoner` skill that generates RFCs from Linear issues will
write to this folder. See `ENG-91` in your tracker for the skill
definition.
