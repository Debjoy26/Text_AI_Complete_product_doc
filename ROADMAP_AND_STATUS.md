# Roadmap and Status - RAGbot

## 1. Status snapshot

Reviewed 2026-11-21 against
[`ragbot-widget` @ `ebc75ec`](https://github.com/Debjoy26/ragbot-widget/tree/ebc75ecaf2c99ce6dcacdbefa3827914b64273b5)
and
[`ragbot-admin-panel` @ `885535c`](https://github.com/Debjoy26/ragbot-admin-panel/tree/885535cd086e923485c78a6330dc4e09332e6084):

- Backend phases P0-P10 are recorded as owner-accepted.
- T25-d real database race checks and T26 single-command unit/integration
  gates remain open carries. P11 evaluation/security/load work and P12
  first-client deployment have not started.
- The backend status records local and hosted migration histories at 027.
- The admin-panel repository contains a working client frontend. A0-A3 are
  recorded accepted; A4 is implemented and locally validated, pending owner
  acceptance. A7.0 design and amendments are accepted in the panel ADR, and
  the provider UI is a mock-backed scaffold. The provider API, registry,
  grant enforcement, and live provider integration are not implemented.
- Widget-key lifecycle, subscription enforcement, production deployment, and
  the canonical approved-answer contract remain open.
- Render remains the API/worker baseline. AWS Docker hosting is undecided.

This is a documentation comparison, not a fresh runtime, security, hosted
configuration, or deployment audit. The widget's ADR 0019 still labels
managed administration proposed, unlike the accepted A7.0 design in the panel
ADR 0003. The provider API owner/repository is unresolved, so no provider
design is operational until the source documents and contract align.

## 2. Backend plan (P0-P12)

| Phase | Scope | Recorded state |
|---|---|---|
| P0 | Product decisions and prerequisites | Accepted |
| P1 | Monorepo/tooling foundation | Accepted |
| P2 | Shared contracts | Accepted |
| P3 | Tenant database, grants/RLS, Storage | Accepted |
| P3.5 | Client-admin backend data layer | Accepted |
| P4 | API foundation and visitor auth | Accepted |
| P5 | Sessions, abuse controls, budgets, idempotency | Accepted |
| P6 | Durable ingestion worker | Accepted |
| P7 | Retrieval and streaming chat | Accepted |
| P7.5 | Caching and latency | Accepted |
| P8 | Browser widget | Accepted |
| P9 | Analysis, entries, retention | Accepted |
| P10 | Operations and deployment hardening | Accepted |
| P11 | Evaluation, security review, load/restore gates | Not started |
| P12 | First client deployment | Not started |

P11 remains behind explicit owner authorization and its defined prerequisites.
T25-d and T26 single-command gates remain open; phase acceptance must not be
misread as closure of those carries.

The managed SaaS/admin extension is outside the accepted P0-P12 baseline.
Documenting it here does not schedule or authorize implementation.

## 3. Admin-panel plan (A0-A8)

| Phase | Scope | Recorded state |
|---|---|---|
| A0 | Resolve product/security decisions and confirm backend contracts | Complete; owner accepted ADRs 0001/0002 |
| A1 | Frontend scaffold and test/build tooling | Complete; local checks recorded |
| A2 | Client Auth, MFA assurance, membership, role states | Complete; local schema-27 Supabase smoke test recorded |
| A3 | Client dashboard, settings, documents, conversations, usage, members | Complete; source contract pinned and panel tests recorded; hosted tenant compatibility remains unverified |
| A4 | Knowledge-gap review and approved-answer workflow | Implemented and locally validated; owner acceptance pending; uses ordinary Markdown document ingestion |
| A5 | Widget installation and public identifier lifecycle | Not started; key contract open |
| A6 | Subscription display and enforcement integration | Not started; semantics/enforcement open |
| A7 | Provider identity, registry, API, audit, client operations | Design accepted/amended and UI scaffold mock-tested; provider backend and live integration not started; API owner unresolved |
| A8 | Accessibility, security, release readiness, deployment | Not started |

The panel status records local lint, typecheck, unit, build, and browser checks;
most browser checks use mocked responses. A2 records a local Supabase schema-27
smoke test. None of these records proves production deployment or hosted-tenant
compatibility. The provider UI is not evidence that provider operations work.

## 4. Cross-repository sequencing

1. Owner resolves A0 decisions and confirms existing backend contracts.
2. Admin client work consumes existing tenant Auth/MFA/RLS/RPC/Storage
   contracts without bypassing them.
3. Any tenant backend gap is separately approved and implemented in
   `ragbot-widget`, with migrations in its single history.
4. The exact contract is documented and verified before the panel integrates.
5. Provider API ownership, registry, tenant grant schema/operations, activation
   enforcement, and audit must be published and tested before live provider
   integration. Panel ADR 0003 accepts the design; widget ADR 0019 remains
   proposed, so this is a source-document conflict to resolve.
6. Release and deployment require their own gates and owner approval.

No phase progresses automatically because documentation exists.

## 5. Evidence and next decision

For detailed backend work history and exact task evidence, see
[`ragbot-widget` status](https://github.com/Debjoy26/ragbot-widget/blob/ebc75ecaf2c99ce6dcacdbefa3827914b64273b5/status.md)
and its [latest checkpoint](https://github.com/Debjoy26/ragbot-widget/blob/ebc75ecaf2c99ce6dcacdbefa3827914b64273b5/docs/reports/LATEST-CHECKPOINT.md).
For admin-panel planning state, see
[`ragbot-admin-panel` status](https://github.com/Debjoy26/ragbot-admin-panel/blob/885535cd086e923485c78a6330dc4e09332e6084/status.md)
and [plan](https://github.com/Debjoy26/ragbot-admin-panel/blob/885535cd086e923485c78a6330dc4e09332e6084/plan.md).

Before any work, confirm current source status, owner authorization, and
unresolved prerequisites; do not rely solely on this snapshot.
