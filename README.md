# RAGbot - Complete Product Documentation

This is the documentation-only product hub for the complete RAGbot system:
the embeddable chatbot and tenant backend, the client administration panel,
and the proposed provider control plane.

It consolidates product intent and system boundaries across the implementation
repositories. It contains no application code, credentials, or deployment
secrets.

## Product at a glance

```text
Client website
  -> static RAGbot widget
  -> visitor API
  -> tenant database, vector search, private document storage, and worker

Client administrators
  -> client admin panel
  -> that client's Supabase Auth, RLS-protected reads, approved RPCs, and Storage

Provider staff
  -> separate provider-admin experience
  -> proposed provider identity, control API, client registry, and audit store
  -> explicitly scoped tenant operations
```

The widget/backend and admin-panel repositories remain separate. Provider
administration, dedicated widget-key lifecycle, and subscription enforcement
are not implemented. The panel has an A4 Markdown submission workflow through
ordinary document ingestion, but its gap linkage is a local convention and
owner acceptance is pending; it is not a dedicated answer-publishing contract.

## Documents

- [Product requirements](PRODUCT_REQUIREMENTS.md) - product scope, roles,
  visitor requirements, and admin requirements.
- [System architecture](SYSTEM_ARCHITECTURE.md) - components, data ownership,
  trust boundaries, and current-versus-proposed topology.
- [User flows](USER_FLOWS.md) - visitor, client-admin, provider-admin, and
  knowledge-gap workflows.
- [Security and privacy](SECURITY_AND_PRIVACY.md) - security invariants,
  permissions, data handling, and unresolved risks.
- [Data and API contracts](DATA_AND_API_CONTRACTS.md) - current documented
  backend interfaces and contracts still to be designed.
- [Deployment and operations](DEPLOYMENT_AND_OPERATIONS.md) - documented
  hosting baseline, operations, and AWS decision boundary.
- [Roadmap and status](ROADMAP_AND_STATUS.md) - backend and admin-panel phase
  status reviewed on 2026-11-21.
- [Decisions and open questions](DECISIONS_AND_OPEN_QUESTIONS.md) - settled
  boundaries, proposed decisions, and owner decisions still needed.
- [Documentation governance](DOCUMENTATION_GOVERNANCE.md) - source-of-truth
  rules for keeping this hub aligned with the two implementation repositories.

## Source repositories

- [`ragbot-widget`](https://github.com/Debjoy26/ragbot-widget) owns the widget,
  visitor API, worker, tenant database/migrations, shared backend contracts,
  and backend security behavior.
- [`ragbot-admin-panel`](https://github.com/Debjoy26/ragbot-admin-panel)
  contains the client-admin frontend and provider UI scaffold, and specifies
  their backend integrations. The provider API and registry are not
  implemented.

See [documentation governance](DOCUMENTATION_GOVERNANCE.md) before treating
this hub as authoritative for an implementation-level contract.

## Status snapshot

Reviewed on 2026-11-21 against:

- [`ragbot-admin-panel` @ `885535c`](https://github.com/Debjoy26/ragbot-admin-panel/tree/885535cd086e923485c78a6330dc4e09332e6084)
- [`ragbot-widget` @ `ebc75ec`](https://github.com/Debjoy26/ragbot-widget/tree/ebc75ecaf2c99ce6dcacdbefa3827914b64273b5)

The backend records P0–P10 as accepted, with T25-d/T26 single-command
verification carries still open. Migration history is recorded at 027 locally
and on hosted development; P11 and P12 have not started. The admin-panel
repository now contains a working client frontend: A0–A3 are recorded
accepted, A4 is implemented and locally validated pending owner acceptance,
and A7 has an accepted design plus a mock-backed provider UI scaffold. The
provider API, registry, grant enforcement, and live provider integration are
not implemented. These are source-document claims, not a fresh runtime or
deployment audit.

The two repositories do not yet agree on the status of the A7 design:
admin-panel ADR 0003 records the A7.0 design and amendments as accepted,
while widget ADR 0019 remains proposed. The provider API owner/repository is
also unresolved. Treat the accepted panel design as a proposal until the
backend owner aligns the ADR and publishes the required contract.
