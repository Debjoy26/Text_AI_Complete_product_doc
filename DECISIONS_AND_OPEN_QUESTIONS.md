# Decisions and Open Questions - RAGbot

## 1. Established ownership boundaries

The current source documentation establishes these boundaries:

- `ragbot-widget` owns tenant migrations, grants/RLS, RPCs, Storage policies,
  shared backend contracts, visitor API, worker, and widget.
- `ragbot-admin-panel` specifies client/provider administration interfaces and
  consumes approved backend contracts; it must not create a competing tenant
  migration history.
- Client admin operations use the tenant Auth, active membership, required
  `aal2`, RLS, reviewed RPCs, and Storage policies.
- Provider administration is a distinct trust domain. A browser must never
  receive service-role credentials or arbitrary tenant credentials.
- Public widget identifiers identify a bot; they do not authenticate an
  administrator or visitor.
- Knowledge-gap answers require human-authored/approved knowledge and the
  ordinary validated indexing and activation lifecycle.
- Tenant schema changes stay in the single backend migration history.

These boundaries do not mean the proposed provider service or admin features
have been implemented.

## 2. Current deployment and product choices

- The documented API/worker baseline is Render.
- The widget is a static JavaScript bundle; the backend docs currently
  describe Vercel static delivery.
- The backend records a per-client Supabase project and deployment model.
- `bot_profiles.enabled` is a bot enablement switch, not a full subscription
  lifecycle.
- Existing `knowledge_gaps` reporting does not publish an answer.
- The existing widget embed contract remains authoritative until an approved
  change is made.

## 3. Owner decisions required

| Topic | Decision needed |
|---|---|
| Provider identity | Identity provider, MFA policy, roles, staff onboarding/recovery, and session model |
| Provider control plane | Owning repository/service, registry schema, hosting, secrets, tenant-targeting and delegation model |
| Cross-client access | Which client data provider staff may see, for what purpose, with what consent and audit |
| Subscription | Plans, status states, start/end semantics, quotas, grace period, manual actions, billing scope, and audit |
| Runtime enforcement | Which operations check status, authoritative source, caching/invalidation, and failure policy |
| Widget identifier | Key/slug format, storage/hashing, bootstrap lookup, host restrictions, rotation, revocation, and unknown-key response |
| Approved answer | Markdown document versus FAQ entity, author/approval rights, deduplication, citations, audit, retention, and indexing status |
| Client membership | Invite, activation, role change, removal, and recovery behavior |
| Admin framework | Frontend framework, package manager, supported Node, build separation, deployment target |
| AWS | Whether to leave Render; EC2 versus ECS/Fargate; isolation; tenant DB location; secrets/monitoring; widget CDN |
| Operations | Data jurisdiction, privacy notice, retention, backup/restore, RPO/RTO, support access, and launch targets |
| Commercial model | Per-client service limits, usage caps, provider costs, service tiers, and any future payment integration |

## 4. Design constraints while decisions are open

- Do not invent an authoritative subscription record or present unknown status
  as active.
- Do not build a dedicated-key UI lifecycle before backend support.
- Do not allow the provider browser to impersonate clients or accept arbitrary
  project IDs/credentials.
- Do not promote visitor questions or model responses directly into knowledge.
- Do not move tenant schema to a provider database or create a second
  migration history without an explicit design.
- Do not treat AWS discussion as authorization to provision or migrate.

## 5. Decision recording

Record each accepted decision in the owning implementation repository using
its ADR conventions, then update this cross-product summary. A summary here
does not itself approve implementation.
