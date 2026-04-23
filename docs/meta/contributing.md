# Contributing To Docs

Rules for the active docs tree:

- Write into the active `docs/` tree.
- Prefer updating an existing source-of-truth document before opening a
  new file.
- Place task guides in `how-to/`, stable facts in `reference/`, and
  reasoning in `explanation/`.
- Keep titles short, clear, and noun-based where possible.
- Architecture decisions are recorded in
  [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md).
  Longer reasoning for a decision belongs in
  [`../explanation/decisions/`](../explanation/decisions/README.md).
- Link with relative paths so the tree stays valid when rendered on any
  host. Do not hard-code the tree root inside a document that lives
  inside the tree itself.
- Avoid embedding credentials, secrets, or real customer data in
  examples.

## Workflow Principles

TabFlow is documentation-first. These principles apply to every change
that touches the shape of the system:

- Any architectural change lands in the relevant `reference/` or
  `explanation/` document **before** the code change.
- When the code and the docs disagree, treat it as a documentation bug
  first. Fix the doc, review the fix, then align the code. Never promote
  the code as the source of truth retroactively.
- An architectural change requires an entry in
  `reference/architecture/decisions.md`. Extended reasoning lives in
  `explanation/decisions/<slug>.md` and is linked from the ADR.
- The docs changelog (`meta/changelog.md`) tracks documentation
  evolution. Product release notes live in a repo-root `CHANGELOG.md`
  when product versioning is established; they do not mix with the docs
  changelog.

## Architectural Change Checklist

Run through this sequence for every change that touches the shape of the
system (new surface, new role, new table, new endpoint, new render mode,
new decision, or a supersede of any of the above):

1. **Update docs first**, in the same pull request as the code:
   - Architecture decision in
     [`../reference/architecture/decisions.md`](../reference/architecture/decisions.md)
     (new entry or supersede).
   - Extended reasoning in
     [`../explanation/decisions/`](../explanation/decisions/README.md)
     when the ADR alone cannot carry the context.
   - Every affected source-of-truth reference document. At minimum:
     [`runtime-surfaces.md`](../reference/architecture/runtime-surfaces.md),
     [`render-modes.md`](../reference/architecture/render-modes.md),
     [`capability-matrix.md`](../reference/architecture/capability-matrix.md),
     [`tenant-api.md`](../reference/api/tenant-api.md),
     [`schema.md`](../reference/database/schema.md),
     [`slos.md`](../reference/architecture/slos.md).
2. **Run docs checks locally.** See
   [`../how-to/ci.md`](../how-to/ci.md) for the lint and link-check
   commands. They MUST pass before the pull request is opened.
3. **Implement the code** against the updated docs. Code follows docs;
   not the other way around.
4. **Run the code checks.** At minimum `dotnet test` and
   `dotnet format --verify-no-changes` across the affected projects.
5. **Verify acceptance criteria.** Every item in
   [`../reference/acceptance-criteria.md`](../reference/acceptance-criteria.md)
   MUST still hold. Add or update items if the change introduces new
   invariants.
6. **Walk the release gate.** See
   [`./release-gate.md`](./release-gate.md). The checklist MUST be
   completed before tagging a release.
7. **Record the change in the appropriate changelog.**
   - User-visible product behaviour → root `CHANGELOG.md`.
   - Documentation evolution → [`./changelog.md`](./changelog.md).
