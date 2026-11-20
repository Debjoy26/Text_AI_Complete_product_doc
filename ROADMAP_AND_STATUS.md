# Roadmap and Status - RAGbot

## 1. Status snapshot

Source documentation status as of 2026-11-20:

- Backend phases P0-P10 are recorded as owner-accepted.
- T25-d real database race checks and T26 single-command unit/integration
  gates remain open carries. P11 evaluation/security/load work has not
  started.
- The backend status records local and hosted migration histories at 027.
- The admin-panel repository is a documentation proposal. No frontend,
  provider API, provider registry, or admin deployment is claimed.
- Provider control plane, widget-key lifecycle, subscription enforcement,
  and admin-approved knowledge publishing remain proposed/open.
- Render remains the API/worker baseline. AWS Docker hosting is undecided.

This snapshot is copied from source documentation, not freshly revalidated.
Use the source repos for any later state.

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
| A0 | Resolve product/security decisions and confirm backend contracts | Pending |
| A1 | Frontend scaffold and test/build tooling | Not started |
| A2 | Client Auth, MFA assurance, membership, role states | Not started |
| A3 | Client dashboard, settings, documents, conversations, usage, members | Not started |
| A4 | Knowledge-gap review and approved-answer workflow | Not started |
| A5 | Widget installation and public identifier lifecycle | Not started; key contract open |
| A6 | Subscription display and enforcement integration | Not started; semantics/enforcement open |
| A7 | Provider identity, registry, API, audit, client operations | Not started; architecture open |
| A8 | Accessibility, security, release readiness, deployment | Not started |

The admin-panel plan is a proposal, not phase acceptance.

## 4. Cross-repository sequencing

1. Owner resolves A0 decisions and confirms existing backend contracts.
2. Admin client work consumes existing tenant Auth/MFA/RLS/RPC/Storage
   contracts without bypassing them.
3. Any tenant backend gap is separately approved and implemented in
   `ragbot-widget`, with migrations in its single history.
4. The exact contract is documented and verified before the panel integrates.
5. Provider features wait for a separately approved control-plane threat model,
   ownership, identity, authorization, and audit design.
6. Release and deployment require their own gates and owner approval.

No phase progresses automatically because documentation exists.

## 5. Evidence and next decision

For detailed backend work history and exact task evidence, see
[`ragbot-widget` status](https://github.com/Debjoy26/ragbot-widget/blob/main/status.md)
and its [latest checkpoint](https://github.com/Debjoy26/ragbot-widget/blob/main/docs/reports/LATEST-CHECKPOINT.md).
For admin-panel planning state, see
[`ragbot-admin-panel` status](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/status.md)
and [plan](https://github.com/Debjoy26/ragbot-admin-panel/blob/main/plan.md).

Before any work, confirm current source status, owner authorization, and
unresolved prerequisites; do not rely solely on this snapshot.
