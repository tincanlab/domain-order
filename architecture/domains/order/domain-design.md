# Domain Architecture: Order

## Context Evidence

- **Workspace**: `ws-init-bopis-order`
- **Domain**: `Order`
- **Domain profile**: `order-management`
- **SA baseline**: `inputs/workstreams/ws-init-bopis-order/architecture-design.yml`
- **Tools used**: `domain-architecture` bootstrap from SA handoff, local repo inspection
- **Generated**: `2026-03-22T20:26:20.8879949Z`

## Domain Boundary

**Bounded context**: BOPIS order lifecycle management from order placement through pickup completion, including order status, cancellation, and pickup readiness.

**Ubiquitous language**:
| Term | Definition |
|---|---|
| BOPIS order | A customer order placed online for store pickup. |
| reservation | The inventory hold created before the order is confirmed. |
| pickup ready | The order has been fulfilled and staged for pickup. |
| pickup completion | The order has been collected by the customer and closed. |

**Anti-corruption layers**:
| External Domain | Strategy | Interface |
|---|---|---|
| Ecommerce | translator | `ifc-product-catalog-api` |
| Inventory | adapter | `ifc-inventory-management-api` |
| Notification | facade | `ifc-notification-api` |
| Party Role Management | adapter | `ifc-pickup-event` |

## Domain Model

### Order (`ord-order`)

**Root entity**: `Order`

**Entities**:
| Entity | Fields | Storage | SID Alignment |
|---|---|---|---|
| Order | `order_id`, `customer_id`, `store_id`, `status`, `created_at`, `updated_at` | PostgreSQL | `CustomerOrder` |
| Order Line Item | `line_item_id`, `order_id`, `sku`, `quantity`, `status` | PostgreSQL | `OrderItem` (local alignment) |
| Order Status History | `event_id`, `order_id`, `from_status`, `to_status`, `changed_at` | PostgreSQL | `CustomerOrder` (history) |

**Value objects**: `OrderId`, `PickupWindow`, `StoreId`

**Domain events**:
| Event ID | Name | Trigger | Payload |
|---|---|---|---|
| `evt-order-placed` | `OrderPlaced` | command | `order_id, customer_id, store_id` |
| `evt-order-ready` | `OrderReadyForPickup` | state transition | `order_id, store_id` |
| `evt-order-picked-up` | `OrderPickedUp` | state transition | `order_id, picked_up_at` |
| `evt-order-cancelled` | `OrderCancelled` | state transition | `order_id, cancel_reason` |

## Workflows

### Order lifecycle (`wf-order-lifecycle`)

**Type**: `state_machine`

**States**: `RECEIVED -> RESERVED -> CONFIRMED -> READY_FOR_PICKUP -> PICKED_UP`, with `CANCELLED` as the terminal exception path.

**Compensating actions**: release inventory reservations when the order is cancelled; keep the order in `READY_FOR_PICKUP` when pickup verification fails and require associate recheck.

**eTOM alignment**: Support Customer Order Management

## Interface Implementations

### `ifc-order-management-api`

**Component**: `comp-order-management`
**Conformance**: `full`

| Operation | Method | Path | Auth | Rate Limit |
|---|---|---|---|---|
| Create BOPIS order | `POST` | `/api/v1/orders/bopis` | `JWT` | `300 rps` |
| Get order | `GET` | `/api/v1/orders/{order_id}` | `JWT` | `300 rps` |
| List customer orders | `GET` | `/api/v1/orders/customer/{customer_id}` | `JWT` | `300 rps` |
| Cancel order | `PUT` | `/api/v1/orders/{order_id}/cancel` | `JWT` | `300 rps` |

### `ifc-order-status-event`

**Component**: `comp-order-management`
**Conformance**: `full`

| Operation | Method | Path | Auth | Rate Limit |
|---|---|---|---|---|
| Publish status event | `EVENT` | `order.events.status` | `service-to-service` | `event-driven` |

## TMF Alignment

**Covered APIs**: `TMF622`
**Uncovered APIs**: `TMF620` is consumed from Ecommerce, not owned here
**SID Mappings**: `Order` -> `CustomerOrder`

## Compliance

| Standard | Controls | Notes |
|---|---|---|
| PCI_DSS | encrypt data in transit and at rest; least privilege service access | No payment card storage in this domain |
| GDPR | data minimization; subject access support | Customer order data retained only as required |
| CCPA | access disclosure; deletion workflow coordination | Deletion requests may require downstream coordination |

## SA Conformance Report

- **NFR budget fit**: `true`
- **Interface conformance**: `conformant`
- **Pattern conformance**: `conformant`
- **Issues**: `none`

## Decisions

| ID | Decision | SA Deviation? | Review Required? |
|---|---|---|---|
| `adr-001` | Order aggregate owns lifecycle transitions and publishes status events | `false` | `false` |
| `adr-002` | Inventory reservation gates order confirmation | `false` | `false` |

## Open Questions

- `Not applicable`
