# System Architecture - RAGbot

## 1. Status and architecture principles

This design distinguishes the documented per-client runtime baseline from
proposed administration/control-plane capabilities. It does not authorize an
AWS migration, provider service, new tenant schema, or production deployment.

Principles:

1. The browser and host website are untrusted.
2. Tenant data and tenant administration are enforced by the tenant backend.
3. Provider-wide authority lives in a separate server-side trust domain.
4. The widget is a static browser bundle; Docker is for server processes.
5. Tenant schema changes have one migration history in `ragbot-widget`.
6. UI visibility never substitutes for server authorization.

## 2. Logical topology

```text
                         PUBLIC / VISITOR PLANE
  Client website
    -> static widget bundle
       -> public tenant bot configuration
       -> visitor API (session token, chat, entries, history)
          -> tenant Supabase Postgres + pgvector
          -> external chat / embedding / moderation providers
          -> durable job queue
             -> tenant worker
                -> private Storage and Postgres
                -> embedding / analysis providers

                         CLIENT ADMIN PLANE
  Client admin browser
    -> client-admin frontend (deployment target not selected)
       -> that tenant's Supabase Auth
       -> RLS-protected reads, approved RPCs, Storage policies

                         PROVIDER CONTROL PLANE (PROPOSED)
  Provider admin browser
    -> separate provider-admin mode/deployment
       -> provider identity and MFA
       -> provider control API
       -> provider-owned client/deployment registry and audit
       -> explicit, scoped tenant operation when permitted
```

The provider control API, registry, and identity are not implemented. The
admin-panel repository records an accepted design for a dedicated provider
identity domain, provider activation gating, and client-issued grants. The
widget repository's ADR 0019 still labels managed administration proposed;
until that source contract is aligned, these remain design-level only. The
provider browser must never connect to arbitrary tenant projects using
service-role credentials.

## 3. Documented runtime deployment baseline

The backend documentation describes per-client isolation:

- One client-specific Supabase project and tenant migration history.
- One configured API service and worker for that client, currently documented
  on Render.
- A static widget bundle delivered from a versioned frontend/static host; the
  backend documentation currently describes Vercel for widget delivery.
- A separate client-admin frontend consuming that tenant's Auth, RLS, RPC,
  and Storage contracts. Its hosting choice is not fixed by the current
  admin-panel proposal.
- External AI services are called by the server, not directly by the browser.

The exact number of hosted projects/services and commercial isolation model
must be revalidated before launch. The client `bot_id` is retained as a scope
even where a tenant has a dedicated database.

## 4. Ownership boundaries

| Component/data | Owner |
|---|---|
| Widget code, public embed contract, visitor API, worker | `ragbot-widget` |
| Tenant tables, grants, RLS, RPCs, Storage policies and migrations | `ragbot-widget`; one history under `supabase/migrations` |
| Tenant settings, documents, conversations, usage, and audit data | That tenant's Supabase project |
| Client admin navigation and presentation | `ragbot-admin-panel` |
| Client authorization | Tenant Auth, membership, MFA assurance, RLS, RPC, and Storage |
| Provider identities, registry, subscription records, provider audit | Proposed provider control plane; owner/repository not selected |
| Product-level cross-repository documentation | This documentation-only repository |

No separate provider registry is added to the tenant schema by default.

## 5. Main request flows

### Visitor chat

1. The widget loads public configuration using only public values.
2. The widget requests a visitor session and receives a short-lived,
   server-issued token scoped to bot/session/expiry.
3. It submits a bounded message with an idempotency key.
4. The API checks session, bot state, limits, rate, budget, and moderation.
5. The API embeds the query with the corpus-compatible model and retrieves
   active, disclosure-eligible chunks.
6. The API builds a bounded server-owned prompt and calls the configured chat
   provider.
7. The API streams allowed output, validates source references, records usage
   and messages, and settles budget.
8. A durable worker performs asynchronous analysis and cleanup work.

### Document indexing

1. An authorized admin registers a document/version through an approved RPC.
2. The browser uploads to a generated pending path permitted by Storage policy.
3. An approved completion call queues a durable indexing job.
4. The worker validates file content and limits, extracts/chunks/embeds, then
   stores inactive data.
5. A transaction activates a complete version and increments corpus version.
6. Failure leaves the previous version active; deletion immediately removes
   retrieval eligibility and queues cleanup.

### Client administration

The admin frontend authenticates against the selected tenant's Supabase Auth.
Private reads and mutations use the tenant's active membership, required
`aal2`, RLS, reviewed RPCs, and Storage policies. The visitor API is not the
ordinary admin write path. Provider credentials are absent from this browser.

### Provider administration (proposed)

The provider API authenticates provider identity and MFA, authorizes the
specific action and client target, performs a narrowly scoped server-side
operation, and records an audit event. The accepted panel design adds two
gates that require backend implementation:

- A tenant cannot self-activate or self-enable while inactive. Widget script
  issuance/serving must be gated on provider activation, with runtime
  enforcement on the authoritative server path.
- Private-data access and updates require a client-issued grant containing
  scope, purpose, and expiry. Clients can inspect and revoke grants; the
  provider API checks the live grant on every gated operation. Grant-related
  actions are audited on both sides.

No grant table/API or provider service is established by the current widget
contract. Any access to private client content remains denied until the
cross-repository contract, migration, API enforcement, and audit behavior are
implemented and verified.

## 6. Trust boundaries

1. Host website, widget runtime, visitor, and request payload.
2. Visitor API and worker with privileged backend credentials.
3. Tenant Postgres/Storage policies and tenant identity.
4. External model/provider services and their data-processing boundaries.
5. Client administrator identity and tenant role.
6. Provider identity/API/registry, distinct from every tenant.
7. Deployment provider, secret store, logging, backups, and support access.

Retrieved documents and model output are untrusted data. Prompt instructions
are not an authorization mechanism. Public chatbot knowledge must be approved
for anonymous disclosure.

## 7. Proposed changes and unresolved topology

- A dedicated per-client widget key is not part of the current embed contract.
  If approved, it remains a public bot identifier, not authentication.
- Subscription and suspension state need an authoritative source and runtime
  checks; the current bot enable flag is not a full subscription system.
- Provider identity, API, registry, audit store, credential delegation, and
  client-content access policy are not designed.
- The backend deployment baseline remains Render API/worker. AWS is a
  considered alternative, not a migrated deployment. EC2 versus ECS/Fargate,
  per-client versus shared isolation, static delivery, secrets, and monitoring
  remain open.
- Client and provider admin builds may share components, but must be separate
  authorization contexts; separate builds alone are not authorization.
