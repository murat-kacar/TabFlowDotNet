# Tunable Parameters

**Status:** Informative. This document is an index of numeric and
boolean knobs whose *current values* are expected to evolve, while
their *shape and meaning* are part of the architecture.

**Maintenance rule:** When a tunable is added, renamed, retired, or
moved from tunable to fixed, this document is updated in the same
pull request as the change. Adjusting the current value of an existing
tunable does not require an ADR; it requires updating the value here
and in the relevant reference document.

---

Some parameters have a stable *role* in the system but no stable
*value*. Session lifetimes, rate budgets, and SLO targets are the
common examples. Treating every tweak to these values as an
architectural change would be noisy; leaving them unlisted would make
it unclear where the current values live.

This document resolves that by listing the tunables explicitly. Each
entry records the canonical source of the current value so there is
one place to look and one place to change.

## Sessions And Tokens

| Parameter | Current | Source |
| --- | --- | --- |
| QR token time-to-live | `60 s` | [`./firmware.md`](./firmware.md), [`./api/tenant-api.md`](./api/tenant-api.md) |
| Customer session default lifetime | `5 min` | [`../explanation/concepts/customer-session-model.md`](../explanation/concepts/customer-session-model.md) |
| Customer session extension window | last `60 s` before expiry | same |
| Customer session extension count limit | `2` (total `15 min`) | same |
| Checkout-proof token time-to-live | same as a fresh QR token | [`./api/tenant-api.md`](./api/tenant-api.md) |

## Rate Budgets

| Parameter | Current | Source |
| --- | --- | --- |
| `POST /api/public/orders` per session | one successful submission per session | [`./api/tenant-api.md`](./api/tenant-api.md) |
| `/g/{token}` retries per remote address | short-window burst limit with `rate_limited` on exceed | [`./api/error-codes.md`](./api/error-codes.md) |
| `/api/public/catalog` probe budget | short-window burst limit | [`./api/tenant-api.md`](./api/tenant-api.md) |

The numeric values of the rate budgets are configured at the host
level and are deliberately not duplicated into docs until production
telemetry exists.

## SLO Targets

All active SLO targets live in
[`./architecture/slos.md`](./architecture/slos.md). The targets there
are tunable; they are expected to be revised after the first
operational quarter against measured performance. Adjusting a target
is a documentation-only change against that file.

## Role Seed

| Parameter | Current | Source |
| --- | --- | --- |
| Tenant seed roles | `owner`, `manager`, `cashier`, `station_device` | [`./architecture/runtime-surfaces.md`](./architecture/runtime-surfaces.md), [`../explanation/concepts/authorization.md`](../explanation/concepts/authorization.md) |
| Platform seed role | single platform admin role at bootstrap | [`./architecture/runtime-surfaces.md`](./architecture/runtime-surfaces.md) |

Adding or removing a role is an architectural change and requires an
ADR (not a tunable update).

## Firmware

| Parameter | Current | Source |
| --- | --- | --- |
| QR matrix side | backend-produced | [`./firmware.md`](./firmware.md) |
| Device WebSocket keepalive | server-pushed `pong` on `ping` | [`./api/tenant-api.md`](./api/tenant-api.md) |
| Device reconnect backoff | firmware-side | [`./firmware.md`](./firmware.md) |

## What Is Not A Tunable

The following are deliberately **not** tunable without an architecture
decision:

- Number of render modes in use (two).
- Number of host processes per side (one).
- Whether platform and tenant Identity stores federate (they do not).
- Whether migrations apply via EF Core (they MUST).
- One-open-bill-per-table invariant.
- Presence of `X-Robots-Tag: noindex, nofollow, noarchive` on HTML
  surfaces.

If a future change needs to move one of these from "fixed" to
"tunable", that itself requires an ADR and a supersede chain in
[`./architecture/decisions.md`](./architecture/decisions.md).
