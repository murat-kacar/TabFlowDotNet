# Authorship Record

**Status:** Informative. This document records authorship of the
original architectural expression captured in this repository. It
does not itself constitute a formal legal registration.

**Maintenance rule:** When a new original architectural element is
introduced, or when the list of supporting documents changes in a way
that affects the evidence chain, this document is updated in the same
pull request as the change.

---

## Rights Holder

- Name: Murat Kacar
- Project: TabFlow
- Repository root: this repository
- Record opened: 2026-04-23

## Scope

This record covers the original architectural expression embodied in
this repository. The elements recognised as characteristic and original
to this work include:

- The customer model bound to a per-table QR scan with single-shot,
  session-terminates-on-order semantics.
- The separation of *QR token*, *customer session*, and *open bill*
  as three distinct concepts with non-overlapping lifetimes.
- The one-open-bill-per-table invariant combined with append-style
  order addition and the split, merge, and reassign operations that
  preserve that invariant.
- The tenant operational surface split across the floor-and-cash
  workspace (`/service`), the waiter PDA (`/pda`), and station
  fulfillment boards (`/stations/{stationCode}`).
- The control-plane / runtime split at the host level (platform host
  versus tenant host) as recorded in AD-0001.
- The unified ASP.NET Core + Blazor Web App stack per side with mixed
  render modes (Static SSR for customer-and-auth surfaces, Interactive
  Server for staff surfaces) as recorded in AD-0002 and AD-0004.
- The authentication model using ASP.NET Core Identity with fully
  isolated platform and tenant Identity stores, recorded in AD-0005.
- The in-process event bus (`Channel<T>`-style) per tenant for
  real-time staff surfaces, recorded in AD-0006.
- The documentation-first workflow expressed in
  [`./contributing.md`](./contributing.md) and the accompanying
  release gate in [`./release-gate.md`](./release-gate.md).

## Supporting Documents

The following documents form the evidence chain for the expression
above and its evolution over time:

- [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md)
- [`../reference/architecture/system-overview.md`](../reference/architecture/system-overview.md)
- [`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md)
- [`../reference/architecture/render-modes.md`](../reference/architecture/render-modes.md)
- [`../reference/architecture/slos.md`](../reference/architecture/slos.md)
- [`../reference/architecture/capability-matrix.md`](../reference/architecture/capability-matrix.md)
- [`../reference/api/tenant-api.md`](../reference/api/tenant-api.md)
- [`../reference/api/error-codes.md`](../reference/api/error-codes.md)
- [`../reference/database/schema.md`](../reference/database/schema.md)
- [`../reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
- [`../explanation/concepts/customer-session-model.md`](../explanation/concepts/customer-session-model.md)
- [`../explanation/concepts/multi-tenancy.md`](../explanation/concepts/multi-tenancy.md)
- [`../explanation/concepts/authorization.md`](../explanation/concepts/authorization.md)
- [`../explanation/decisions/why-blazor-unified.md`](../explanation/decisions/why-blazor-unified.md)
- [`../explanation/decisions/why-postgresql-17.md`](../explanation/decisions/why-postgresql-17.md)
- [`../explanation/alignment-with-pos-conventions.md`](../explanation/alignment-with-pos-conventions.md)
- The git commit history of this repository, including authorship
  metadata and timestamps.
- The public release history of tagged versions, including timestamps
  recorded by the hosting platform.

## Declaration

The architectural narrative, source tree shape, data model, flow
design, and documentation structure expressed in this repository are
original to the rights holder named above. This record, in
combination with the commit history, published release timestamps,
and any external registrations the rights holder may file separately
(copyright registration, notarised timestamps, patent filings, or
equivalent), is intended to strengthen the evidentiary chain of
ownership. It does not itself substitute for any formal registration.

## License

Usage of the contents of this repository is governed by the top-level
`LICENSE` file. This record does not grant any license.
