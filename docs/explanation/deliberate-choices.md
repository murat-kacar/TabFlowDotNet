# Deliberate Choices

**Status:** Informative. This document indexes product behaviours that
may look like gaps or bugs at first reading but are, in fact,
intentional. Each item points at the normative source where the
behaviour is defined.

**Maintenance rule:** When a deliberately-chosen behaviour is
introduced, revised, or removed, this document is updated in the same
pull request as the change.

---

The shortest way to reduce "is this a bug?" tickets is to list the
design choices that reliably surprise people on first read. Nothing
in this document is new; every item is covered by a decision, a
reference document, or an acceptance criterion. This page just pulls
them together for a reader who has not yet formed a full picture.

## Customer Surface

- **There is no public catalog.** `/menu` returns a locked state
  without a current customer session. Menu data, pricing, and
  availability MUST NOT be visible to an unauthenticated browser.
  See
  [`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md)
  and AC-025 in
  [`../reference/acceptance-criteria.md`](../reference/acceptance-criteria.md).
- **Customer sessions are single-shot.** A successful order submission
  closes the originating customer session; a follow-up order requires
  a fresh QR scan. See AC-036 and the rationale in
  [`./concepts/customer-session-model.md`](./concepts/customer-session-model.md).
- **There is no customer-initiated logout endpoint.** Customer session
  termination is a property of the table, not the browser. See
  AC-026 and the Customer Session section of
  [`../reference/api/tenant-api.md`](../reference/api/tenant-api.md).
- **Every order submission requires a fresh second QR scan.** The
  first scan opens the session; the second scan produces a
  checkout-proof token consumed by `POST /api/public/orders`. See
  AC-031 and AC-032.
- **No payment or card vaulting lives inside TabFlow.** Cashier
  workflow takes payment outside the product. See the divergence
  section of
  [`./alignment-with-pos-conventions.md`](./alignment-with-pos-conventions.md).

## Back-Office Surface

- **Admin and staff surfaces do not talk over an internal HTTP API.**
  Razor components call dependency-injected application services
  directly. `/api/admin/**` is not exposed. See the Absent Surfaces
  section of [`../reference/api/tenant-api.md`](../reference/api/tenant-api.md).
- **Platform and tenant Identity stores never federate.** A platform
  admin cannot sign into a tenant console and a tenant user cannot
  sign into the platform console. See AD-0005 in
  [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md)
  and AC-003, AC-012.
- **There is exactly one open bill per table at any time.** First
  order opens a bill; subsequent orders append to it. Split, merge,
  and reassign preserve this invariant for every table involved.
  See AC-040 to AC-045.
- **The last active `owner` on a tenant cannot be deactivated or
  demoted.** The UI hides the option; the server enforces it. See
  AC-015.

## Runtime And Transport

- **Two render modes, not four.** Only Static SSR and Interactive
  Server are used. Interactive WebAssembly and Interactive Auto are
  deliberately not in scope. See AD-0004 and
  [`../reference/architecture/render-modes.md`](../reference/architecture/render-modes.md).
- **One host process per side, not per function.** Platform runs one
  process; a tenant runs one process. There is no separate
  `*-api.service` and `*-web.service` split. See AD-0003.
- **The event bus is in-process per tenant.** There is no external
  message broker. See AD-0006 and
  [`./concepts/operational-surfaces.md`](./concepts/operational-surfaces.md).

## Data Shape

- **EF Core migrations are the only schema path.** There is no
  handwritten idempotent `ALTER` fallback applied at startup. See
  AD-0008 and AC-082.
- **Platform and tenant databases share nothing at the data level.**
  Tenant business data never appears in the platform database;
  control-plane data never appears in a tenant database. See AC-080
  and AC-081.

## Web Posture

- **No search indexing.** Every HTML surface carries
  `X-Robots-Tag: noindex, nofollow, noarchive` and `robots.txt`
  disallows all user agents on every host. SEO is deliberately
  absent from the product's priority order. See the Web Indexing
  Posture section of
  [`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md)
  and AC-090 to AC-092.

## Firmware

- **The firmware never generates QR codes locally.** The server
  produces the bit matrix and the device renders it. See AC-062,
  AC-063, and [`../reference/firmware.md`](../reference/firmware.md).

## Related

- [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md)
- [`../reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
- [`./alignment-with-pos-conventions.md`](./alignment-with-pos-conventions.md)
