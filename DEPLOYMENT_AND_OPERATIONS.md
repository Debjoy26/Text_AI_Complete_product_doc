# Deployment and Operations - RAGbot

## 1. Deployment status

This is a product-level deployment plan, not a record that infrastructure was
provisioned. The documented backend baseline is Render for API/worker and a
static host for the widget (currently documented as Vercel). Supabase hosts
tenant Postgres, pgvector, Auth, and private Storage.

AWS Docker hosting has been discussed but is not an approved migration. The
widget is a static JavaScript bundle, not a Docker service.

## 2. Current documented deployment shape

Per client, the documented topology is:

| Component | Purpose | Current documented hosting direction |
|---|---|---|
| Widget | Browser JavaScript bundle | Static/versioned hosting; backend docs describe Vercel |
| Visitor API | Sessions, chat, entries, history | Render web service |
| Worker | Durable indexing, analysis, cleanup | Render background worker |
| Tenant database | Auth, Postgres, pgvector, RLS, RPCs | Client-specific Supabase project |
| Knowledge objects | Private originals and related content | Supabase Storage |
| Client admin panel | Settings, documents, conversations, usage, member workflows | Separate frontend; hosting not fixed in its proposal |
| Provider admin/control plane | Provider identity, registry, audited lifecycle actions | Proposed; host/repository not selected |

The admin-panel frontend is no longer documentation-only: the panel status
records a React/Vite client app and an `/app/provider` scaffold. The provider
scaffold currently shares the client build artifact as an interim state; the
accepted panel architecture requires separate provider trust-mode deployment
before live provider wiring. Neither client-panel nor provider-panel production
hosting is recorded as selected or deployed.

## 2.1 Admin-panel implementation and environment status

The panel repository records A0-A3 accepted, A4 implemented/local-tested
pending owner acceptance, and an A7 mock-backed provider UI scaffold. A2's
authentication smoke test used a local Supabase schema-27 stack; A3/A4 and
provider browser checks use mocked responses. The panel README requires a
tenant-specific public configuration, Auth redirect allowlist, email/password
Auth, and TOTP for local sign-in. These are not evidence of hosted-tenant
compatibility or production configuration.

The panel does not create tenant membership on signup. Until a membership
invitation/management contract is implemented, a trusted workspace owner
activates membership through Supabase tooling. Hosted Auth policy, SMTP,
domains, secrets, and static hosting still need explicit release configuration.

Production capacity, connection limits, provider quotas, and cost are finite.
Use measured workload and provider limits; no unlimited scalability or SLA is
implied.

## 3. AWS option (decision pending)

If AWS is selected, first choose:

- EC2 versus ECS/Fargate for API and worker containers.
- Shared versus per-client runtime isolation and network boundaries.
- Tenant database placement and connectivity; do not assume Supabase moves to
  AWS.
- Secret storage, IAM/task roles, key rotation, TLS, logging, alerts, and
  incident access.
- Widget static delivery (for example S3 plus CloudFront) and versioned
  rollback.
- Image build, registry, patching, health checks, autoscaling limits, backup,
  and recovery responsibilities.
- Cost limits, regions, data residency, and owner-approved operational
  responsibilities.

Docker applies to server-side processes. Do not package the visitor widget as
a long-running container.

## 4. Release and compatibility

- Version widget and API deliberately; preserve compatible API behavior during
  widget rollout/rollback.
- Database changes are forward-safe, tested migrations in the owning backend
  repository.
- Deployments require health verification, error/latency monitoring, rollback
  instructions, and a reviewed change record.
- Client and provider admin modes must not share browser-held privileged
  authority; deploy them as separate trust modes. The current provider UI
  scaffold shares the client artifact and is not the final deployment boundary.
- Never infer production readiness from a successful build or a green test
  alone.

## 5. Monitoring and operations

Record request/job IDs and monitor:

- API availability, readiness, latency, request failures, and provider
  fallback frequency.
- Queue age, lease/retry behavior, indexing failures, and active-version
  changes.
- Rate denials, usage/budget reservations, estimated cost, and provider-side
  quotas.
- Database/Storage quotas, connection pressure, backup status, and restore
  results.
- Admin authorization failures and provider audit events without sensitive
  content.

Redact tokens, credentials, document bodies, and sensitive chat content from
logs and support reports. Fail visibly on missing critical configuration.

## 6. Backup, retention, and recovery

- Agree retention and deletion across conversations, source excerpts,
  documents, chunks, caches, audit records, and backups.
- Verify Storage object backup/restore separately from database backup.
- Rehearse restore and compatibility with API/worker versions before a
  production commitment.
- Define idempotent cleanup, abandoned-upload handling, and reservation/job
  recovery.
- Record recovery objectives and owner approvals; none are implied by this
  planning document.

## 7. Deployment approvals

AWS provisioning, production deployment, hosted schema writes, real provider
calls, and client-data access require the appropriate explicit owner approval.
This documentation hub grants none of them.

The admin-panel's accepted A7 design also requires provider activation to gate
widget issuance/serving and client-issued grants for provider private-data
access or updates. These controls are not implemented in the widget backend;
its ADR 0019 remains proposed. Do not treat either the UI scaffold or an
accepted panel design as deployment readiness.
