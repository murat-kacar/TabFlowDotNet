# Docs Changelog

This changelog tracks the evolution of the documentation tree only.
Product release notes live in the repo-root
[`../../CHANGELOG.md`](../../CHANGELOG.md) and are kept separate from
this file. See [`./contributing.md`](./contributing.md) for the split
rationale.

## Unreleased

- Initial documentation baseline for TabFlow: architecture decisions
  AD-0001..AD-0008, runtime surfaces, render modes, API surface,
  authorization, customer session model, SLOs, capability matrix,
  glossary, firmware reference, and the operational how-to set.
- Added RFC 2119 normativity and per-document maintenance-rule
  headers to every source-of-truth reference document.
- Added an architectural-change checklist to
  [`./contributing.md`](./contributing.md).
- Expanded the tenant API error model to a Problem Details envelope
  with an extension `code` field, and added
  [`../reference/api/error-codes.md`](../reference/api/error-codes.md)
  as the enumerated code vocabulary.
- Added [`../reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
  as the flat, testable behaviour contract.
- Added [`./release-gate.md`](./release-gate.md) as the pre-tag
  release checklist.
- Added [`../explanation/alignment-with-pos-conventions.md`](../explanation/alignment-with-pos-conventions.md)
  recording the industry references behind baseline choices.
- Added [`./authorship.md`](./authorship.md) as the architectural
  authorship record.
- Added a *Web Indexing Posture* section to
  [`../reference/architecture/runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md).
- Added [`../explanation/deliberate-choices.md`](../explanation/deliberate-choices.md)
  indexing the product's deliberately surprising behaviours.
- Added [`../reference/tunable-parameters.md`](../reference/tunable-parameters.md)
  listing knobs whose values may evolve without an ADR.
