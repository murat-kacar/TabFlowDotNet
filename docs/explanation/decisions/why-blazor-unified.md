# Why Blazor As The Unified Stack

This document is the reasoning behind
[`../../reference/architecture/decisions.md`](../../reference/architecture/decisions.md)
AD-0002 (ASP.NET Core 10 and Blazor Web App) and AD-0003 (one host process
per side).

The short version: one stack, one host per side, one set of idioms. The
web tier and the API tier collapse into one project and the team tracks a
single vendor release cadence.

## What Blazor Web App Provides

Blazor Web App in .NET 10 is a server-first full-stack framework. It renders
static HTML, renders interactive server components, and ships with enhanced
navigation and enhanced forms that cover cases that usually justify a
separate SPA tier.

The relevant properties for TabFlow are:

- A component can be Static SSR or Interactive Server declaratively.
- A component can call into dependency-injected services directly, without
  an HTTP hop.
- Authentication is native cookie auth through ASP.NET Core Identity.
- Authorization uses standard attributes and policies.
- Antiforgery, model binding, localization, and validation are framework
  primitives.

Combining this with AD-0003 (one host process per side) keeps the web
surface and the domain service layer inside one project. There is no
backend-for-frontend proxy layer and no duplicated validation layer.

## Convention Over Innovation

Every choice in TabFlow follows the convention of its ecosystem rather than
invent new machinery. The monolith direction is aligned with current
industry practice:

- Shopify has continued to describe itself as a majestic monolith after
  years of scale growth.
- Stack Overflow served global traffic from a single .NET monolith for many
  years and continues to document that shape publicly.
- The `MonolithFirst` position, popularized by Martin Fowler and others,
  argues for moving to services only under real pressure, not as a default.
- Microsoft's own Blazor Web App template ships as a single project. The
  framework does not encourage splitting into API and web tiers unless the
  team has a specific cross-boundary reason.

Orchard Core, the most relevant .NET multi-tenant reference in the same
family of problems TabFlow solves, also runs each tenant inside a single
ASP.NET Core host.

## Ecosystem Shape

A single-stack .NET shape produces:

- One package manager: `dotnet` and NuGet.
- One analyzer and formatter: the .NET analyzer set with
  `dotnet format`.
- One test runner: `xUnit` (or `NUnit`) paired with `bUnit` and
  `WebApplicationFactory` for component and integration testing.
- One build orchestrator: `dotnet build` and `dotnet publish`.
- One dependency graph tied to the .NET release cadence, with clearly
  labeled long-term support versions.

## Trade-offs Accepted

### Blazor Server circuit cost

Interactive Server requires a SignalR circuit and server-held state per
connected client. For public customer traffic this would be expensive.
AD-0004 (mixed render modes) addresses this by keeping customer-facing
surfaces on Static SSR and confining Interactive Server to authenticated
staff surfaces where the circuit cost is justified.

### Learning curve

Contributors coming from other component ecosystems need to learn Blazor
component authoring. The cost is a short ramp; the gain is a single concept
of what a component is, one idiomatic state model, and one debugging
toolchain per team member.

### External API surface

A future native mobile client or a deliberate third-party integration needs
an explicit HTTP API surface. AD-0003 accepts that this is additive and not
a retrofit: the domain layer lives in a shared package, so a second host
project that exposes HTTP endpoints against the same application services is
a contained project, not a refactor.

## Related

- [`../../reference/architecture/decisions.md`](../../reference/architecture/decisions.md)
- [`../../reference/architecture/system-overview.md`](../../reference/architecture/system-overview.md)
- [`../../reference/architecture/render-modes.md`](../../reference/architecture/render-modes.md)
