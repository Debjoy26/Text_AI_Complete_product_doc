# Security and Privacy - RAGbot

## 1. Security objective

Protect client data and administrative authority while making only
client-approved knowledge available to anonymous visitors. The widget runs in
an untrusted host page and is not a secret-bearing security boundary.

## 2. Identity and authorization

### Visitors

- A bot slug, public widget identifier, Supabase publishable key, and website
  origin are not credentials.
- Visitor access uses a short-lived server-issued token bound to bot, session,
  and expiry.
- Every history/chat operation validates session ownership and token scope.
- Rate limits, challenge policy, and budgets apply independently of any
  presumed visitor identity.

### Client administrators

- Require a valid tenant Supabase Auth session, active membership, allowed
  role, and required `aal2` assurance.
- The accepted panel login design uses email/password. Self-registration,
  where enabled by tenant Auth policy, creates no membership; confirmation
  and password-reset links are not sign-in links. Membership activation
  remains an operator-managed tenant action until an approved in-panel
  membership contract exists.
- Enforce authorization in PostgreSQL RLS, approved RPCs, and Storage policies,
  not only in the panel.
- Service-role access bypasses RLS; privileged routes must perform explicit
  ownership and role checks.
- Client admin browsers receive no service-role, provider, AI-provider, or
  visitor-signing secrets.

### Provider staff (accepted panel design; backend not implemented)

- Provider identity, MFA, role, target-client scope, and action permission are
  checked by a provider service on every request.
- Provider users are not silently inserted as admins in every tenant.
- The provider browser cannot connect to arbitrary client projects with
  service-role credentials.
- Every cross-client lifecycle operation is scoped and audited.
- Access to private transcripts or document bodies requires a separate
  purpose, permission, and audit policy.
- The accepted panel design further requires a tenant-issued grant for
  provider access to private data or updates. A grant carries scope, purpose,
  and expiry, is visible and revocable by the client, and must be checked live
  by the provider API on every gated operation. Grant changes and gated
  operations require audit on both sides.
- Provider activation must gate widget issuance/serving and tenant
  self-enable. This must be enforced by an authoritative server path, not by
  hidden UI.

The grant and activation requirements are accepted in admin-panel ADR 0003
amendments, but are not implemented in the widget backend. Widget ADR 0019
still marks the managed-admin boundary proposed. Do not treat the panel
decision as a production authorization control.

## 3. Data and content safeguards

- Anonymous grants expose only the dedicated public bot configuration.
- Public retrieval uses only active documents explicitly allowed for public
  answer disclosure.
- Document uploads are validated by content, size, path, and worker-side
  extraction rules; filenames and MIME declarations are not trusted.
- Retrieved documents, visitor prompts, and model output are untrusted data.
- Browser-supplied context, system prompts, roles, assistant messages, or
  usage values are rejected/ignored as authority.
- Citations are restricted to retrieved chunk IDs and validated source
  metadata.
- Plain-text rendering is the default. Optional Markdown must be sanitized;
  raw HTML, script URLs, remote images, and embedded frames are excluded.
- Contact cards are built from server-approved public configuration, never
  guessed by the model.
- Knowledge-gap answers require human authorship/approval and the ordinary
  validated indexing/activation process. No automatic learning.

## 4. Abuse, billing, and consistency controls

- Atomic database-backed rate counters and budget reservations; no
  process-local-only accounting.
- Idempotency keys cannot spend twice; body mismatch under a key is rejected.
- A provider cannot be switched silently after public tokens are emitted.
- Embedding model changes require corpus-compatible reindexing; no silent
  encoder switch.
- Document replacement activates atomically; a failed replacement leaves the
  prior active version intact.
- Missing limits, authorization context, or required subscription state must
  follow an approved fail-closed policy.
- Subscription/suspension enforcement must be server-authoritative. The UI or
  script alone cannot revoke service.

## 5. Secrets, logs, and privacy

- Keep service-role, visitor-signing, Turnstile, AI-provider, and cloud
  credentials in server-side secret stores only.
- Do not commit credentials or expose them in browser bundles, prompts,
  screenshots, logs, error responses, or ordinary support reports.
- Redact tokens and document bodies; minimize raw conversation content in
  telemetry and provider audit metadata.
- Collect only data necessary for service, security, and agreed analytics.
- Define retention, deletion, backup, provider-processing, jurisdiction, and
  disclosure policies before production use.
- Tenant data remains in its tenant-owned project according to the current
  backend model; do not make hidden provider-side copies.

## 6. Security verification expectations

Test direct backend/RPC/Storage access, not only UI behavior:

- Anonymous user, nonmember, inactive/revoked member, `aal1`, analyst, admin,
  and owner permission matrix.
- Cross-tenant ID substitution, forged visitor/session IDs, and unauthorized
  history access.
- Role/MFA checks on every admin RPC and Storage operation.
- Upload spoofing, unsafe documents, prompt injection, HTML/URL injection,
  invented citations, and cross-bot cache/data leakage.
- Rate/budget races, duplicate delivery, idempotency conflict, and lease
  takeover behavior.
- Provider-client credential separation, target restriction, provider MFA,
  grant enforcement/audit, activation gating, and audit evidence once provider
  infrastructure exists.
- Secret scanning and browser-bundle inspection.

This checklist is a design baseline, not a completed security certification.

## 7. Decisions still needed

Provider identity and recovery, provider content access, subscription state
and fail-closed behavior, public-key lifecycle, retention/jurisdiction, and
support access remain owner decisions. See
[decisions and open questions](DECISIONS_AND_OPEN_QUESTIONS.md).
