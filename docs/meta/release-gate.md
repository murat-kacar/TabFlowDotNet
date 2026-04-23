# Release Gate

**Status:** Normative. The keywords **MUST**, **MUST NOT**, **SHALL**,
**SHOULD**, and **MAY** are used per [RFC 2119][rfc2119] and [RFC 8174][rfc8174].

**Maintenance rule:** When a release-blocking concern is added, relaxed,
or retired, this document is updated in the same pull request as the
change.

[rfc2119]: https://www.rfc-editor.org/rfc/rfc2119
[rfc8174]: https://www.rfc-editor.org/rfc/rfc8174

---

This checklist is the last-stop verification before a release tag moves.
Every item MUST be ticked on a clean environment against the exact
commit that would ship. A single unchecked item blocks the release.

Run the gate in two passes:

1. **Local pass** — against the commit on the release branch.
2. **Staging pass** — against a deployed staging tenant at the same
   commit. Items marked *staging* apply only here.

The gate is deliberately longer than CI. CI catches regressions
continuously; the gate catches the things that only make sense right
before users see them.

## Documentation

- [ ] [`reference/architecture/decisions.md`](../reference/architecture/decisions.md)
  reflects every architecture decision that landed in this release.
- [ ] [`reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md),
  [`render-modes.md`](../reference/architecture/render-modes.md),
  [`capability-matrix.md`](../reference/architecture/capability-matrix.md),
  [`tenant-api.md`](../reference/api/tenant-api.md),
  [`schema.md`](../reference/database/schema.md), and
  [`slos.md`](../reference/architecture/slos.md) match the shipped
  behaviour.
- [ ] [`reference/api/error-codes.md`](../reference/api/error-codes.md)
  covers every code returned by the shipped HTTP surfaces.
- [ ] [`reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
  has an item for every new invariant introduced in this release.
- [ ] [`meta/changelog.md`](./changelog.md) records the documentation
  evolution for this release.
- [ ] Root `CHANGELOG.md` records the product evolution for this
  release.

## Checks

- [ ] `dotnet test TabFlow.sln` passes.
- [ ] `dotnet format --verify-no-changes` passes across the solution.
- [ ] `dotnet ef migrations has-pending-model-changes` reports no
  pending changes for platform and tenant contexts.
- [ ] Docs linter (`markdownlint`) passes on `docs/`, `README.md`,
  `CHANGELOG.md`.
- [ ] Link checker (`lychee`) passes on the same set.
- [ ] SAST and dependency audit pipelines are green.

## Acceptance

Every statement in
[`reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
MUST hold on the build under test. If any item cannot be verified by an
automated test, the gate MUST include a recorded manual pass with a
ticket link.

- [ ] Platform access items (AC-001 to AC-004) verified.
- [ ] Tenant access items (AC-010 to AC-015) verified.
- [ ] Customer join and session items (AC-020 to AC-026) verified.
- [ ] Order submission items (AC-030 to AC-036) verified.
- [ ] Table and bill invariants (AC-040 to AC-045) verified.
- [ ] Station board items (AC-050 to AC-052) verified.
- [ ] Device channel items (AC-060 to AC-063) verified.
- [ ] Auditability items (AC-070 to AC-072) verified.
- [ ] Data residency items (AC-080 to AC-082) verified.
- [ ] Web posture items (AC-090 to AC-092) verified.
- [ ] Observability items (AC-100 to AC-102) verified.

## Smoke Scenarios (*staging*)

Run each of these end-to-end on a staging tenant provisioned from the
release commit. The scenario MUST complete without manual recovery.

- [ ] Customer join and order: scan QR at `/g/{token}` → `/menu` opens
  with the cart empty → add two items → submit with a fresh second
  scan → customer session closes → order appears on the relevant
  station board within the SLO latency budget.
- [ ] Floor and cash: cashier opens `/service`, sees the just-closed
  session's bill as open on the table, closes the bill → table returns
  to empty state and remaining customer sessions on the table are
  invalidated.
- [ ] Bill split: two tables, one with an open bill, split one line
  item to the second table → both tables respect the one-open-bill
  invariant.
- [ ] Device rotation: a table's ESP32 device shows the rotated QR
  within the token TTL; a stale QR at `/g/{token}` is rejected with
  `token_used` or `token_expired`.
- [ ] Provisioning: provision a fresh tenant from the platform
  console; the new tenant boots with seed roles (`owner`, `manager`,
  `cashier`, `station_device`) and the one-time owner credentials are
  delivered through the provisioning job payload.

## Observability (*staging*)

- [ ] `curl -I https://<tenant>/menu` returns
  `X-Robots-Tag: noindex, nofollow, noarchive`.
- [ ] `curl https://<tenant>/robots.txt` returns a fully-disallowed
  policy.
- [ ] `curl -I https://<tenant>/health/ready` returns `200` when the
  tenant database is reachable.
- [ ] `curl -i -X POST https://<tenant>/api/public/orders` with an
  invalid body returns an `application/problem+json` response carrying
  both `code` and `traceId`.
- [ ] SLO dashboards show no error-budget burn from the previous
  release window.

## Rollback Plan

- [ ] The previous release tag is reachable and deployable without
  schema rollback.
- [ ] Any migration included in this release has a documented rollback
  path, or is declared forward-only with a noted reason.
- [ ] Feature flags introduced in this release default to the
  pre-release behaviour on fresh boot.

## Sign-Off

- [ ] Release owner has recorded the gate pass on the release ticket
  with a link to the commit.
- [ ] At least one additional reviewer has co-signed the gate.
