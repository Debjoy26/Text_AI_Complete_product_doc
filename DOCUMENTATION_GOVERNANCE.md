# Documentation Governance

## 1. Purpose

This repository provides one readable product-level view across the chatbot
backend/widget and its administration panel. It is documentation-only and
must not become an accidental implementation, migration, or deployment
repository.

## 2. Source-of-truth hierarchy

For implementation-level questions, use this order:

1. **Runtime behavior and tenant data/security contracts:** current source
   code, tests, and migrations in
   [`ragbot-widget`](https://github.com/Debjoy26/ragbot-widget).
2. **Admin screens, roles, and panel journeys:** current specifications and
   implementation in
   [`ragbot-admin-panel`](https://github.com/Debjoy26/ragbot-admin-panel),
   subject to the backend contracts above.
3. **Product-level relationship and cross-system narrative:** this repository.
4. **Open or proposed behavior:** explicitly label as proposed; do not treat as
   implemented or owner-approved.

If this hub conflicts with an implementation repo, the implementation repo is
authoritative for its owned contracts. Report the conflict and update this hub
after owner review; do not silently change a backend or admin contract here.

## 3. Update process

When an owned source document or implementation changes:

1. Identify affected product requirements, flows, architecture, security,
   data/API, deployment, roadmap, and decision pages.
2. Preserve the difference between implemented, documented, proposed,
   accepted, and released.
3. Link to exact source documents and dated evidence where possible.
4. Mark unknowns as open; never fill gaps by inference.
5. Review cross-repository links and check Markdown/whitespace before
   committing documentation.

## 4. Status language

- **Implemented:** verified in source code/tests or migration evidence.
- **Documented:** specified in an authoritative source document; implementation
  may differ or not exist.
- **Proposed:** product direction not approved for implementation.
- **Accepted:** owner accepted a defined phase/decision; this does not alone
  imply deployment.
- **Released/deployed:** only when there is explicit deployment evidence.
- **Open:** decision or gate remains unresolved.

Do not use a phase table or requirement row as proof of feature completion.

## 5. Content and privacy rules

- No secrets, live credentials, raw visitor tokens, private client data, or
  unredacted conversation/document bodies.
- Do not duplicate raw logs, transcripts, or customer data into this repo.
- Reference evidence by safe path or link instead of copying sensitive
  artifacts.
- Keep tenant API/schema exactness in the backend repository; this hub should
  summarize rather than create a competing contract.
- No source code or infrastructure manifests belong here unless the owner
  changes the repository's docs-only purpose.

## 6. Snapshot metadata

Update the review date and exact source revisions when refreshing the status
snapshot in [README.md](README.md) and [ROADMAP_AND_STATUS.md](ROADMAP_AND_STATUS.md).
The 2026-11-21 snapshot compares `ragbot-admin-panel` at
`885535cd086e923485c78a6330dc4e09332e6084` with `ragbot-widget` at
`ebc75ecaf2c99ce6dcacdbefa3827914b64273b5`. It is not a live synchronization
mechanism or an independent runtime audit.

When source documents disagree, record the mismatch, identify which source
owns the implementation contract, and avoid resolving it by inference. In
particular, distinguish an accepted admin-panel design from a provider or
tenant-backend capability that has not been specified and implemented there.
