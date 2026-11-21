# Data and API Contracts - RAGbot

## 1. Authority and scope

This document summarizes interfaces for product-level orientation. Exact SQL,
RLS, function signatures, Zod schemas, request fields, and error behavior must
be taken from the current `ragbot-widget` source and its migration history.
The admin panel must not infer a contract from a screen mockup.

## 2. Tenant data model (high level)

Each client has a tenant Supabase project under the current documented model.
`bot_id` remains an explicit scope within that project.

| Domain | Important relations/concepts |
|---|---|
| Membership and bot | `admin_memberships`, `bot_profiles`, `client_settings`, `public_bot_config` |
| Knowledge | `documents`, `document_versions`, `document_chunks`, private Storage objects |
| Visitor state | `visitor_sessions`, `conversations`, `messages`, `message_sources` |
| Analysis and forms | `conversation_analysis`, `data_entries` |
| Async work | `jobs`, leases, attempts, progress, deduplication |
| Abuse and cost | `request_runs`, `rate_limit_buckets`, `budget_reservations`, `usage_events` |
| Audit and caches | `audit_logs`, query embedding/moderation/answer caches |

`public_bot_config` is intentionally separated from private bot/settings and
conversation data. Anonymous users must not gain access to other relations.
Provider registry data is not part of the tenant schema by default.

## 3. Document and conversation lifecycles

### Knowledge version

`pending upload -> queued/indexing -> validated and embedded -> atomically
active` or `failed`. The active pointer changes only when the replacement is
complete. Deletion immediately removes retrieval eligibility, then performs
durable cleanup.

### Visitor turn

`session validation -> idempotent request reservation -> moderation ->
retrieval -> response/stream -> citation validation -> persistence and budget
settlement`. Duplicate requests do not spend twice. Interrupted streams are
reported as partial/failed rather than silently regenerated.

### Knowledge gap

`aggregate report -> human review -> admin-authored content -> validated
ingestion -> indexing -> active retrieval source`. The existing gap report is
not itself an answer-publishing API.

## 4. Documented visitor API surface

| Method and route | Purpose |
|---|---|
| `GET /health/live` | Minimal process liveness |
| `GET /health/ready` | Bounded readiness status |
| `POST /v1/sessions` | Create/reuse a scoped visitor session under abuse controls |
| `POST /v1/chat` | Stream a grounded response using visitor bearer auth and idempotency |
| `POST /v1/entries` | Submit an explicitly approved and validated form entry |
| `GET /v1/conversations/:id` | Bounded same-session history, if enabled |

The admin panel does not use the visitor API for ordinary administrative
operations.

## 5. Chat event contract (conceptual)

The documented SSE event family is:

- `meta` - request/conversation/message identifiers.
- `delta` - approved text increment.
- `sources` - validated display references.
- `handoff` - reason, localized configured message, and public contact
  information.
- `done` - terminal successful/partial state.
- `error` - safe error code, request ID, and permitted retry hint.

Exact event schemas and replay behavior must be confirmed against current
shared contracts. Once output has started, provider fallback must not silently
switch providers.

## 6. Client administration interface

Client-admin reads and writes use:

1. RLS-protected reads.
2. Documented browser-callable, role-scoped RPCs.
3. Storage operations permitted by the tenant's Storage policies.

The panel must not mutate protected tables directly. Existing capabilities
documented by the backend include administrative paths for documents,
settings, bot enablement, permitted conversation data, member operations, and
knowledge-gap reporting. Verify exact names, parameters, and roles from the
current backend schema before integration.

The existing `knowledge_gaps` capability is a bounded aggregate report. It is
not an answer-publishing API. The panel's A4 implementation submits an
admin-authored `public_answer` Markdown document through the ordinary
register/upload/complete document pipeline; indexing and activation remain
authoritative. The panel records a gap association using a title plus a
source-gap blockquote convention, not a durable backend relation. The report
does not provide language, a raw conversation reference, or a pagination
cursor; its range and row limits are bounded. These implementation details do
not establish a dedicated answer contract, citation guarantee, or live
provider behavior.

The pinned panel review describes a 92-day maximum range and a 1–200 row
limit. The backend omits the `blocked` group from the analyst aggregate, but
analysts retain direct RLS reads to permitted message records; aggregate
filtering is not a transcript-privacy boundary.

## 7. Contracts not yet established

| Capability | Current documentation | Required design |
|---|---|---|
| Dedicated widget key | Existing embed uses public bot/config values | Issuance, hash/storage, lookup/bootstrap, rotation, revocation, optional host policy, cache invalidation |
| Provider service | The panel accepted a provider API/registry design, but neither is implemented | Identity/MFA/roles, ownership, tenant targeting, credential handling, operations, audit |
| Subscription | Bot enablement exists; it is not a subscription lifecycle | Plan/status/period/limits/grace semantics, authoritative state, runtime checks, outage policy |
| Suspension | No full provider suspension contract | Server-side enforcement for session/chat/index paths and restoration/audit semantics |
| Approved answers | Knowledge-gap reporting exists | Markdown-vs-FAQ representation, author/approval, authorization, indexing status, audit, retention, citation |
| Provider-to-tenant calls | No delegation model is selected | Narrow operation set, least privilege, target verification, audit, no browser-held privileged key |
| Client-held grants | No grant table or API is established by the current backend contract | Tenant-owned scope, purpose, expiry, issue/revoke operations, and live provider-API enforcement with audit on both sides |
| Provider activation gate | No provider activation or script-issuance contract is established | Authoritative provider-active state, no tenant self-activation, widget issuance/serving gate, and runtime enforcement |

Do not build UI controls that imply these contracts already exist.

The admin-panel ADR 0003 records the activation and grant designs as accepted
amendments, while `ragbot-widget` ADR 0019 remains proposed. This status
difference is unresolved; neither design is evidence of a shipped schema,
RPC, provider API, or runtime check.

## 8. Compatibility and change process

1. Record a cross-repository contract gap.
2. Obtain owner approval for the backend task and threat model.
3. Implement tenant schema changes only in `ragbot-widget`'s single
   `supabase/migrations` history.
4. Test grants, RLS, RPC, Storage, worker, and API behavior at the owning repo.
5. Publish the exact contract for panel integration.
6. Update this product hub after the source docs and implementation evidence
   are authoritative.

Do not create a competing tenant migration history in the admin or documentation
repository.
