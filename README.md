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
administration, dedicated widget-key lifecycle, subscription enforcement, and
the reviewed-answer publishing workflow are product proposals, not claims of
implemented features.

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
  specifies the client and provider administration experiences and their
  backend integrations. Its current baseline is documentation-only.

See [documentation governance](DOCUMENTATION_GOVERNANCE.md) before treating
this hub as authoritative for an implementation-level contract.

## Status snapshot

As recorded in the source documentation on 2026-11-20:

- The backend records P0-P10 as owner-accepted. T25-d and T26 single-command
  verification carries remain open; P11 has not started.
- The backend status records local and hosted migration histories at 027.
- The admin-panel repository has a proposed documentation baseline, not a
  frontend, provider API, or provider registry implementation.
- Render remains the documented API/worker baseline. AWS Docker hosting is an
  open architecture decision; the widget is a static browser bundle.

These are source-document status notes, not a fresh audit or verification by
this documentation repository. Check the linked repositories for changes
after the snapshot date.
