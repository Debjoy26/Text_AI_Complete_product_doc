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

- The documented API/worker baseline is Render. The AWS alternative is
  recorded as Proposed in backend ADR 0021 (2026-11-24).
- The widget is a static JavaScript bundle; the backend docs currently
  describe Vercel static delivery. Note 2026-11-24: Vercel Hobby is
  non-commercial, so client-facing delivery needs Pro or static hosting
  (e.g. S3 + CloudFront, as per ADR 0021).
- The backend records a per-client Supabase project and deployment model.
- `bot_profiles.enabled` is a bot enablement switch, not a full subscription
  lifecycle.
- Client sign-in is email plus password with mandatory TOTP MFA; new accounts
  stay inert until the provider side activates membership
  ([panel ADR 0004](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/docs/decisions/0004-password-login.md)).
- Knowledge-gap review plus approved Markdown answers are implemented in the
  panel (A4, owner-accepted); answers become retrievable only after backend
  activation.
- The provider design is accepted panel-side with activation gating (no
  active state, no servable script; tenants cannot self-activate) and a
  client-held grant mechanism for private-content access
  ([panel ADR 0003](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/docs/decisions/0003-a7-provider-baseline-proposal.md)).
  The provider API home is decided (isolated server piece of
  `ragbot-widget`); the widget repo's ADR 0019 remains Proposed.
- The existing widget embed contract remains authoritative until an approved
  change is made.

## 3. Owner decisions: settled vs still required

Settled panel-side on 2026-11-21 (see the linked panel ADRs; the widget
repo's ADR 0019 still records these as Proposed):

| Topic | Settled position |
|---|---|
| Provider identity | Dedicated staff domain, TOTP MFA, server-side roles, documented break-glass recovery |
| Provider control plane | Isolated server piece of `ragbot-widget`; provider-owned registry, never in tenant schema |
| Cross-client access | Default-deny on private content; client-held grants (scope, purpose, expiry, instant revoke); every access audited both sides |
| Approved answer | Markdown document via the ordinary document pipeline; human approval required; retrievable only after activation |
| Client membership | Self-registration allowed; accounts inert until provider-side activation; TOTP stays mandatory |
| Admin framework | React + strict TypeScript + Vite + React Router; Node 26.13.1; static builds; provider split deployment due at wiring |

Still requiring owner decisions:

| Topic | Decision needed |
|---|---|
| Subscription | Plans, status states, start/end semantics, quotas, grace period, manual actions, billing scope, and audit |
| Runtime enforcement | Which operations check status, authoritative source, caching/invalidation, and failure policy |
| Widget identifier | Key/slug format, storage/hashing, bootstrap lookup, host restrictions, rotation, revocation, and unknown-key response |
| Client membership | In-panel invitation flow, role changes, and removal (activation itself is settled). Opened 2026-11-24: signup policy conflicts — panel ADR 0004 allows self-registration (inert until activation) while the backend P12 runbook prescribes signup off plus `make first-admin` |
| AWS | Whether to adopt backend ADR 0021 (ECS/Fargate + S3/CloudFront); isolation; tenant DB location; secrets/monitoring; widget CDN |
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
