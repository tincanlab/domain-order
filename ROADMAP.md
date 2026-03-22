# ROADMAP (Domain)

Owner: Domain Architect (DA)

Purpose: a single, developer-facing plan for the Order domain that reconciles the BOPIS handoff into stable implementation targets.

## Scope

- Domain key: `order`
- Time horizon: `current quarter + next quarter`
- Upstream sources tracked (evidence only): `inputs/workstreams/ws-init-bopis-order/...`

## Current Status

- Health: `yellow`
- Current milestone: `bootstrap-complete`
- Next milestone: `implementation-planning`
- Key risks:
  - `implementation targets are not yet published`
  - `cross-domain interface owners still need implementation details`

## Roadmap

### Milestones

- `bootstrap-complete`: `2026-q1`: Establish canonical DA design artifacts and routing catalog.
- `implementation-planning`: `2026-q2`: Derive concrete implementation targets and work items.

### Epics / Workstreams

- `order-lifecycle-core`: `Order domain team`: Publish the order service implementation target and status event handling.
- `cross-domain-integrations`: `Order + Ecommerce + Inventory + Notification`: Lock interface conformance and anti-corruption layers.

### Backlog (Prioritized)

- P0: finalize `implementation-catalog.yml` work items when implementation repos are identified
- P1: add domain ADRs for reservation, cancellation, and pickup completion behavior
- P2: expand runtime observability and error handling guidance

## Implementation Targets (Developer Interface)

Developers should implement against these domain-owned targets:

- Canonical domain design: `architecture/domains/order/domain-design.md` and `architecture/domains/order/domain-design.yml`
- Component specs: `architecture/domains/order/component-specs.yml`
- Interface contracts (upstream): `inputs/workstreams/ws-init-bopis-order/interface-contracts.yml`

## Decision Log

- `2026-03-22`: Bootstrapped the Order domain repo from the BOPIS SA handoff.
