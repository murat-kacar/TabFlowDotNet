# Alignment With POS And Restaurant Conventions

**Status:** Informative. This document captures *why* the baseline shape
looks the way it does and which established industry patterns it
follows. Normative behaviour lives in the `reference/` tree; this
document records the rationale and the external references backing
each choice.

---

TabFlow's domain shape is not invented from scratch. Most of the
back-office surface, the kitchen pipeline, the menu model, and the
permission axis follow conventions already in use across modern
restaurant and hospitality software. This document makes those
references explicit so future changes can be measured against the same
industry baseline rather than against personal preference.

The customer side — QR-first, single-shot session, no public catalog —
is the deliberate divergence. That divergence is called out in its own
section below.

## Back Office Split

TabFlow separates customer surfaces from staff and admin surfaces at
the route and authentication level (see
[`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md)).
A dashboard acts as a light overview; heavy operational work lives on
dedicated routes such as `/service`, `/pda`, `/console/catalog`, and
`/console/tables`.

This mirrors the information architecture of modern back-office
consoles:

- [Lightspeed K-Series Back Office][lightspeed-back-office] —
  dashboard as overview, operations distributed into purpose-built
  screens.
- [Lightspeed K-Series Order Management screen][lightspeed-order-mgmt]
  — a dedicated screen for live order flow, separate from the menu or
  floor plan.

[lightspeed-back-office]: https://k-series-support.lightspeedhq.com/hc/en-us/articles/360054950934-Introduction-to-the-Back-Office
[lightspeed-order-mgmt]: https://k-series-support.lightspeedhq.com/hc/en-us/articles/360050328594-Understanding-the-Order-management-screen

## Floor Plan And Table Identity

Tables in TabFlow carry a stable identity and a layout hint. The layout
lives on `/console/tables` for management and on `/service` for live
operations.

Reference patterns:

- [Square for Restaurants floor plans][square-floor-plans] —
  section-based floor plan with coordinate-like placement.

[square-floor-plans]: https://squareup.com/help/us/en/article/6427-create-a-floor-plan

## Per-Table Open Bills

TabFlow enforces that a table has at most one open bill at a time, and
the first order on a table with no open bill opens a new one. Further
operations preserve that invariant through bill split, merge, and
reassign.

Reference patterns:

- [Square for Restaurants open tickets (checks) model][square-open-tickets]
  — per-table open check, append subsequent orders.
- [Square move, merge, and reassign checks][square-reassign] —
  split/merge/reassign while the one-open-per-table invariant stays
  intact.
- [Square seat tracking][square-seats] — seat-level attribution inside
  a single open bill.

[square-open-tickets]: https://squareup.com/help/us/en/article/5337-use-open-tickets-with-square
[square-reassign]: https://squareup.com/help/us/en/article/8166-comp-void-and-reassign-checks-with-square-for-restaurants
[square-seats]: https://squareup.com/help/us/en/article/8583-manage-seats-in-your-restaurant

## Menu, Modifiers, And Special Requests

The menu model carries items, modifier groups, modifier options, and a
free-form special-request field on each order line. Bulk actions and
version history live on `/console/catalog`.

Reference patterns:

- [Toast items database][toast-items] — items, categories, visibility,
  versioning.
- [Toast modifier groups and modifiers][toast-modifiers] — nested
  modifier groups with option-level pricing deltas.
- [Toast special requests on items][toast-special-requests] —
  per-line free-form note.

[toast-items]: https://support.toasttab.com/en/article/Using-the-Items-Database
[toast-modifiers]: https://support.toasttab.com/en/article/Creating-Modifier-Groups-and-Modifiers-1492803987509
[toast-special-requests]: https://support.toasttab.com/en/article/Adding-Special-Requests-to-Items-1493007020949

## Kitchen Display Pipeline

TabFlow routes each order item to a preparation station and exposes a
ticket-board surface for station operators. Item-level state (pending,
in progress, done, cancelled) drives a derived order-level state.

Reference patterns:

- [Toast Kitchen Display System][toast-kds] — station-scoped routing,
  item-level state, derived order state.

[toast-kds]: https://support.toasttab.com/en/article/Get-Started-With-the-Kitchen-Display-System

## Granular Role And Permission Matrix

Tenant roles (`owner`, `manager`, `cashier`, `station_device`) are the
seeded baseline; finer-grained permissions (for example
`Console:ManageUsersBelowOwner`) gate individual console surfaces.

Reference patterns:

- [Square employee permissions][square-permissions] — granular
  permission groups attached to roles, scoped per surface.

[square-permissions]: https://squareup.com/help/us/en/article/5822-employee-permissions

## Audit Log As First-Class Surface

Tenant and platform audit logs are first-class surfaces
(`/console/audit` on the tenant, `/audit` on the platform). Every
admin mutation writes an entry before responding success. This follows
the standard back-office posture across Square, Toast, and Lightspeed
consoles, where administrative actions are expected to be traceable
after the fact.

## Deliberate Divergence: Customer Surface

Most POS products model the customer as a menu-on-table browsing
experience or as a server-initiated order flow. TabFlow treats the
customer browser as a short-lived, single-shot session bound to a
freshly-scanned table QR:

- `/menu` has no public catalog without a session.
- Ordering requires a second table QR scan as checkout proof.
- Successful order submission closes the session immediately.
- No customer-initiated logout endpoint exists because session
  termination is a property of the table, not the browser.

This posture is the characteristic product choice. It is not a gap
against industry conventions; it is an intentional divergence traded
for three things:

- Physical presence at the table becomes a precondition for ordering,
  which removes a class of fraud and SEO-scraping pressure.
- Menu and pricing are never a public catalog, which simplifies the
  operational surface.
- No checkout, payment terminal integration, or card vaulting exists
  inside TabFlow. Payment is taken by cashier workflow outside the
  product.

## Related

- [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md)
- [`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md)
- [`./concepts/customer-session-model.md`](./concepts/customer-session-model.md)
- [`./concepts/authorization.md`](./concepts/authorization.md)
