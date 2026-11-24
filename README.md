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
backend implementation, dedicated widget-key lifecycle, and subscription
enforcement remain proposals, not claims of implemented features. The
reviewed-answer publishing workflow (A4) is implemented in the panel and
owner-accepted.

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
  status as recorded on 2026-11-20.
- [Decisions and open questions](DECISIONS_AND_OPEN_QUESTIONS.md) - settled
  boundaries, proposed decisions, and owner decisions still needed.
- [Documentation governance](DOCUMENTATION_GOVERNANCE.md) - source-of-truth
  rules for keeping this hub aligned with the two implementation repositories.

## Source repositories

- [`ragbot-widget`](https://github.com/Debjoy26/ragbot-widget) owns the widget,
  visitor API, worker, tenant database/migrations, shared backend contracts,
  and backend security behavior.
- [`ragbot-admin-panel`](https://github.com/Debjoy26/ragbot-admin-panel)
  implements the client administration experience (A0–A4 accepted) and a
  provider superadmin scaffold awaiting its backend; it consumes approved
  backend contracts and owns no tenant migrations.

See [documentation governance](DOCUMENTATION_GOVERNANCE.md) before treating
this hub as authoritative for an implementation-level contract.

## Status snapshot

As recorded in the source documentation on 2026-11-21, updated 2026-11-24
for backend P11 progress (see the linked repos for the authoritative state):

- The backend records P0-P10 as owner-accepted. T25-d and T26 single-command
  verification carries remain open. P11 started 2026-11-24: live Gemini eval
  23/24, citation-marker fix, security checklist executed; load p95, fallback
  eval, and throwaway restore rehearsal remain carried.
- The backend status records local and hosted migration histories at 027.
  Default chat model moved to `gemini-3.5-flash-lite` (2.5 retired for new
  keys; paid-tier $0.30/$2.50 per 1M — see backend ADR 0020).
- The admin panel records A0–A4 as owner-accepted and implemented (client
  login is email plus password with mandatory TOTP; see
  [ADR 0004](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/docs/decisions/0004-password-login.md)).
  The provider design is accepted with activation-gating and client-held
  grant amendments ([ADR 0003](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/docs/decisions/0003-a7-provider-baseline-proposal.md));
  its screens are scaffolded and the provider backend (API, registry, staff
  identity) is not started. The API home is decided: isolated server piece
  of `ragbot-widget`; that repo's ADR 0019 remains Proposed pending owner
  acceptance. A signup-policy conflict (panel self-registration vs backend
  signup-off) is recorded for owner decision.
- Render remains the documented API/worker baseline. The AWS alternative
  (ECS/Fargate + S3/CloudFront) is recorded as Proposed in backend
  [ADR 0021](https://github.com/Debjoy26/ragbot-widget/blob/main/docs/decisions/0021-aws-deployment-baseline.md);
  the widget is a static browser bundle.

These are source-document status notes, not a fresh audit or verification by
this documentation repository. Check the linked repositories for changes
after the snapshot date.
