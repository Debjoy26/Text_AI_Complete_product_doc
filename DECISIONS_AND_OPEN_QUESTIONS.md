# Decisions and Open Questions - RAGbot

## 1. Established ownership boundaries

The current source documentation establishes these boundaries:

- `ragbot-widget` owns tenant migrations, grants/RLS, RPCs, Storage policies,
  shared backend contracts, visitor API, worker, and widget.
- `ragbot-admin-panel` contains the client-admin frontend and a mock-backed
  provider scaffold; it consumes approved backend contracts and must not
  create a competing tenant migration history.
- Client admin operations use the tenant Auth, active membership, required
  `aal2`, RLS, reviewed RPCs, and Storage policies.
- Provider administration is a distinct trust domain. A browser must never
  receive service-role credentials or arbitrary tenant credentials.
- Public widget identifiers identify a bot; they do not authenticate an
  administrator or visitor.
- Knowledge-gap answers require human-authored/approved knowledge and the
  ordinary validated indexing and activation lifecycle.
- Tenant schema changes stay in the single backend migration history.
- Client sign-in uses email/password by accepted admin-panel ADR 0004;
  registration alone does not grant tenant membership or access.

These boundaries do not mean the proposed provider service or deferred admin
contracts have been implemented.

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

## 3. Open decisions and cross-repository gates

| Topic | Status / remaining decision |
|---|---|
| Provider identity | The panel accepted a dedicated provider identity domain, TOTP MFA, server-checked roles, and break-glass recovery in ADR 0003; the widget ADR 0019 remains proposed, and no identity service is implemented |
| Provider control plane | A provider API and provider-owned registry are accepted as the panel design direction; owning repository/service, schema, hosting, secrets, and tenant-targeting implementation remain unresolved |
| Cross-client access | Panel ADR 0003 adds client-issued grants with scope, purpose, expiry, client visibility/revocation, live API checks, and audit on both sides; no tenant grant schema or provider enforcement exists, and widget ADR 0019 has not adopted this amendment |
| Provider activation | Panel ADR 0003 requires provider activation before widget script issuance/serving and prohibits tenant self-activation/self-enable while inactive; authoritative state and runtime enforcement are not implemented or specified in widget ADR 0019 |
| Subscription | Plans, status states, start/end semantics, quotas, grace period, manual actions, billing scope, and audit |
| Runtime enforcement | Which operations check status, authoritative source, caching/invalidation, and failure policy |
| Widget identifier | Key/slug format, storage/hashing, bootstrap lookup, host restrictions, rotation, revocation, and unknown-key response |
| Approved answer | The panel implements Markdown submission through ordinary document ingestion; backend-level linkage, deduplication, citations, audit, retention, and any dedicated FAQ contract remain open |
| Client membership | Until the invitation contract is implemented, activation is an operator-managed tenant action; invite, role change, removal, and recovery behavior remain open |
| Admin framework | React, strict TypeScript, Vite, React Router, Node 26.13.1 and npm 11.20.0 are accepted in admin ADR 0002; hosting account, domains, and separate production build/release remain open |
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

## 5. Cross-repository status mismatch

The admin-panel repository's ADR 0003 records A7.0 and its activation/grant
amendments as accepted on 2026-11-21. The current `ragbot-widget` ADR 0019 is
still marked Proposed and says owner acceptance plus a bounded plan are
required. The admin-panel status also says the provider API repository is not
selected and the provider backend is 0% implemented. Therefore:

- Treat provider identity, activation gating, and client-held grants as accepted
  **panel design**, not as a released or backend-enforced security contract.
- Resolve the ADR status and choose the provider API owner/repository before
  implementing provider operations or tenant grant migrations.
- Do not imply that the current widget blocks self-activation, gates script
  issuance, or checks client grants.

## 6. Decision recording

Record each accepted decision in the owning implementation repository using
its ADR conventions, then update this cross-product summary. A summary here
does not itself approve implementation.
