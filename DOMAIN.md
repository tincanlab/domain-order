# DOMAIN

Domain architecture repo entrypoint. Owns the order domain baseline, its developer routing catalog, and the canonical DA-owned design artifacts.

## Read First

1. This file - domain context and navigation

## Parent

- [ENTERPRISE](https://github.com/tincanlab/ea-repo/blob/main/ENTERPRISE.md)

If enterprise level is absent in your organization, replace with `Not applicable`.

## Critical File Contract

- Keep required section headings from this template.
- Do not rename or delete required sections.
- Keep this file concise: identity, routing semantics, and links.
- Put detailed/mutable operational values in canonical artifacts and link them here.
- If a required section has no content, keep it and write `Not applicable`.

## Knowledge Store Layout

```text
DOMAIN.md                                          <- you are here
AGENTS.md                                          <- repo-specific agent instructions
ROADMAP.md                                         <- DA-owned developer-facing plan
domain-implementations.yml                         <- canonical implementation routing catalog
.openarchitect/
`-- cascade-state.yml                              <- (optional) governance gates + pinned refs
architecture/
`-- domains/
    `-- order/
        |-- domain-design.md                       <- narrative domain design
        |-- domain-design.yml                      <- structured domain design
        |-- component-specs.yml                    <- refined component specs
        |-- data-model.yml                         <- domain data model
        `-- workflows.yml                          <- domain workflows / saga flows
inputs/
`-- workstreams/
    `-- ws-init-bopis-order/
        |-- source.yml                             <- upstream provenance + lineage
        |-- architecture-design.yml               <- SA baseline snapshot
        |-- component-specs.yml                   <- SA handoff snapshot
        `-- interface-contracts.yml               <- SA handoff snapshot
```

## Canonical Artifacts

- `domain-implementations.yml` (canonical `WORK_ITEM_ID` / `API_ID` routing selector)
- `architecture/domains/order/*.yml` (domain design, component specs, data/workflow details)
- `inputs/workstreams/ws-init-bopis-order/source.yml` (upstream handoff provenance and routing context)

## Routing

`implementation_id` or `api_id` -> `domain-implementations.yml` -> `repo_url` + `repo_path` + optional `repo_entrypoint` + optional `repo_git_ref`

## Upstream Inputs

- Prefer `inputs/workstreams/ws-init-bopis-order/` because `WORKSTREAM_ID` is the selector/routing key for this bootstrap.
- `source.yml` identifies upstream repo URL + commit/tag + captured_at + reason, and includes `solution_key`, `initiative_id`, `workstream_id`, and `handoff_ref`.
- Keep only the current upstream feed in the workstream folder; use Git history for prior states unless explicit snapshot/version folders are later required.

## Policy

- Treat selector inputs as authoritative (`WORKSTREAM_ID`, `implementation_id`).
- Fail-closed on inactive status by default.
