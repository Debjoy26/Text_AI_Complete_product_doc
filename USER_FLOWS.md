# User Flows - RAGbot

## 1. Visitor asks a question

1. Visitor opens the embedded widget and reads the AI/privacy notice.
2. The widget loads the bot's public configuration.
3. If a new session is required, the widget completes the configured
   verification/challenge flow and requests a session.
4. The API issues a short-lived session token scoped to the bot and session.
5. Visitor submits a question; the widget sends no browser-authored system
   prompt, retrieved context, usage, or trusted assistant history.
6. The API applies limits, abuse controls, moderation, retrieval, and grounded
   response generation.
7. The widget displays streamed text and validated citations.
8. On a gap or handoff condition, it displays the configured contact card.
9. The visitor can continue, stop generation, or start a new session.

The service must not say that a human was contacted unless a separate,
implemented contact service confirms that outcome.

## 2. Client admin signs in

1. User signs in through the tenant's configured Supabase Auth project.
2. The panel checks the active tenant membership and required `aal2`
   assurance.
3. Backend RLS and every privileged RPC/Storage policy re-check the relevant
   identity, membership, role, and MFA conditions.
4. On expiry, revocation, or lost assurance, the panel clears sensitive
   in-memory state and presents an explicit re-authentication/denial state.

UI route guards are presentation only.

## 3. Client admin manages a document

1. Admin selects an approved file and enters metadata.
2. The panel requests document/version registration through a permitted RPC.
3. The backend returns a generated pending Storage path.
4. The browser uploads only to that path using tenant Storage policies.
5. The panel completes the upload through the approved RPC.
6. The worker validates, extracts, chunks, embeds, and reports safe progress.
7. The backend activates the version atomically when all validation succeeds.
8. The panel shows active/failed state from the backend, never from optimistic
   UI assumptions.
9. On failure, prior active knowledge remains available; retry/re-index/delete
   use authorized backend operations.

## 4. Client admin changes bot settings

1. The panel loads server-authoritative settings and `settings_version`.
2. It presents only whitelisted fields.
3. The admin submits changes with the expected version.
4. The backend validates authorization and the merged settings, audits the
   change, and rejects stale versions.
5. The panel reports success only after backend confirmation; stale edits are
   surfaced as conflicts rather than silently overwritten.

## 5. Client admin reviews a knowledge gap (proposed end-to-end feature)

1. The panel displays an authorized, bounded aggregate of frequent unanswered
   questions.
2. The admin reviews permitted context without unrestricted transcript access.
3. The admin authors or approves a response or updates an existing source.
4. The content is submitted through the approved tenant knowledge lifecycle.
5. The worker reports indexing state.
6. Retrieval can use the content only after successful atomic activation.

No visitor question or model response is automatically published. Whether the
content is a Markdown document or FAQ entity remains open.

## 6. Client admin installs the widget

1. The panel displays only the currently supported public embed configuration.
2. It generates a script snippet containing public bot/configuration values.
3. It explains that a public identifier is not an admin credential or visitor
   token.
4. It does not expose a dedicated-key rotate/revoke control unless and until
   the backend supports the corresponding lifecycle.

## 7. Client admin reviews subscription status

The panel may display provider-managed status read-only when an authoritative
backend contract exists. Client admins do not set provider subscription
status. Unknown or stale status must be shown as unknown/stale, not active.
Current subscription lifecycle and enforcement are not established.

## 8. Provider staff manages a client (proposed)

1. Staff authenticates to the provider trust domain and completes provider
   MFA.
2. The provider API verifies staff role, requested operation, and target client
   against a provider-owned registry.
3. The API performs only the specifically authorized operation.
4. The API records actor, target, action, outcome, timestamp, and safe
   metadata.
5. The UI displays the authoritative result and audit correlation.

The browser cannot supply arbitrary project references, credentials, or
deployment URLs. Suspension/restoration must affect server-side runtime
authorization; hiding a UI element is insufficient.

## 9. Shared interface states

Every admin data screen must distinguish loading, empty, ready, denied,
session-expired/MFA-required, validation error, stale conflict, quota/rate
limit, backend outage, and explicitly reported partial data. Errors must not
be disguised as empty data or successful mutations.
