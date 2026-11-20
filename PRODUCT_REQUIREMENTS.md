# Product Requirements - RAGbot

## 1. Document status

This document combines the product requirements for the visitor-facing RAG
chatbot and its administration experiences. It describes intended behavior;
requirements are not evidence that a feature is implemented or released.

The visitor/widget requirements W-01 through W-16 originate in the
`ragbot-widget` product baseline. The administration requirements ADM-01
through ADM-20 originate in the `ragbot-admin-panel` proposal. Requirements
marked proposed depend on owner decisions and backend contracts.

## 2. Product vision

RAGbot is a managed, client-branded chatbot that a business can embed on its
website with a script tag. It answers from approved tenant knowledge, cites
retrieved sources, and routes visitors to configured human contact channels
when it cannot safely answer.

Client administrators manage their own chatbot and knowledge. Provider staff
may eventually manage the provider's client registry and service lifecycle
through a separate, audited control plane. Client, visitor, and provider
identities are distinct trust domains.

## 3. Users and roles

| User | Scope | Main responsibilities |
|---|---|---|
| Visitor | One chatbot session | Ask questions and receive grounded answers, citations, or a contact handoff |
| Client owner | One client tenant | Manage bot, knowledge, settings, and permitted members |
| Client admin | One client tenant | Manage authorized bot and knowledge capabilities |
| Client analyst | One client tenant | Review only permitted conversations, usage, and aggregated knowledge gaps |
| Provider operator | Provider control plane | Inspect deployment/client status and perform specifically granted operations |
| Provider super-admin | Provider control plane | Manage provider staff and authorized client lifecycle actions |
| FDE/operator | Operational systems | Provision, configure, monitor, evaluate, and support deployments under approved procedures |

Provider roles are proposed and must not be represented as tenant memberships.
The user interface is not an authorization boundary.

## 4. Product boundaries

### Backend and widget repository

`ragbot-widget` is the implementation source for the browser widget, visitor
API, worker, tenant Supabase migrations, RLS/grants, Storage policies, shared
contracts, and tenant-admin backend operations.

### Administration repository

`ragbot-admin-panel` specifies client and provider administration journeys.
Client administration consumes approved tenant contracts. It does not own
tenant migrations, the visitor API, indexing, or privileged credentials.
Provider administration requires a separately designed provider service.

### This documentation repository

This repository is a product-level documentation hub only. It does not deploy
or implement any component and does not override the implementation
repositories' exact API, schema, or security contracts.

## 5. Goals

1. Provide a one-script installation for a client website.
2. Answer only from approved, retrieval-eligible knowledge and provide
   validated citations.
3. Give visitors safe, multilingual responses and useful contact handoff when
   evidence is missing, a question is out of scope, or service is unavailable.
4. Isolate each client's configuration, documents, conversations, usage, and
   administrative access.
5. Let client administrators manage their chatbot through server-enforced
   authorization.
6. Make indexing durable and replacement atomic so failed updates do not
   replace previously active knowledge.
7. Support bounded provider fallback, abuse controls, budgets, and operational
   visibility.
8. Provide provider-wide lifecycle management only through a separately
   authorized and audited control plane.
9. Allow human-reviewed answers to frequent knowledge gaps, without automatic
   promotion of visitor or model text.

## 6. Non-goals

- Autonomous tools that place orders, issue refunds, book appointments, or
  execute arbitrary actions.
- Live human-agent takeover or a claim that a person was notified.
- Automatic model training or learning from visitor messages.
- Unrestricted tenant-to-tenant access or provider credentials in a browser.
- Treating a widget identifier, website origin, or publishable database key as
  authentication.
- Billing-provider integration or automatic payment collection in the initial
  admin-panel scope.
- Claiming universal scanned-PDF/OCR support without a chosen and tested
  pipeline.
- Replacing Render with AWS, or changing the current embed contract, by
  implication of these product requirements.

## 7. Visitor and backend requirements

| ID | Requirement | Acceptance intent |
|---|---|---|
| W-01 | Script-tag installation | Install on agreed host sites without editing their application code |
| W-02 | UI isolation | Host styles do not break the widget; widget styles do not alter host elements |
| W-03 | Streaming | Ordered events render incrementally; aborts and interruptions are explicit |
| W-04 | Grounded retrieval | Search only active, eligible tenant documents with the corpus-compatible embedding model |
| W-05 | Missing knowledge | Do not invent an answer; provide an appropriate contact handoff |
| W-06 | Bot identity | Apply configured name, tone, scope, and language policy |
| W-07 | Citations | Display only server-validated sources tied to retrieved content |
| W-08 | Provider fallback | Use bounded retries/fallback only before public tokens are emitted; never silently change embedding space |
| W-09 | Moderation | Apply configured input/output moderation policy |
| W-10 | Analysis | Run summaries, sentiment, and intent asynchronously, without delaying answer startup |
| W-11 | Trusted history | Browser cannot forge assistant messages, system context, usage, or trusted conversation records |
| W-12 | Limits | Bound payload, history, context, concurrency, per-session/IP rates, and budgets |
| W-13 | Data entries | Accept only explicitly submitted, validated, approved form data |
| W-14 | Safe rendering | Plain text by default; sanitized Markdown only when explicitly enabled |
| W-15 | Durable indexing | Retry jobs safely and activate a replacement atomically; preserve old active knowledge on failure |
| W-16 | Contact routing | For no-knowledge, out-of-scope, human-requested, blocked, repeated-miss, or unavailable cases, show server-configured contact details |

The handoff must not imply that a human has been notified. Contact information
comes from approved server configuration, not model output.

## 8. Client and provider administration requirements

| ID | Requirement | Acceptance intent |
|---|---|---|
| ADM-01 | Client login and MFA | Private actions require the tenant's required MFA assurance |
| ADM-02 | Tenant isolation | Changing a client/bot identifier cannot expose or alter another tenant |
| ADM-03 | Bot settings | Edit only backend-whitelisted settings and surface stale-version conflicts |
| ADM-04 | Documents | Upload, inspect indexing state, retry/re-index, and delete through approved paths |
| ADM-05 | Conversation and usage review | Show only data permitted by the caller's tenant role |
| ADM-06 | Members | Manage members only where the membership and Auth contracts allow |
| ADM-07 | Installation | Generate a safe script snippet using only approved public embed values |
| ADM-08 | Widget identifier lifecycle | Create/rotate/revoke a public identifier only after backend support exists |
| ADM-09 | Subscription display | Show provider-managed status read-only to client members |
| ADM-10 | Knowledge-gap report | Show permitted aggregate unanswered-question data |
| ADM-11 | Reviewed answer | Allow an authorized human to author/approve knowledge; retrieval waits for successful indexing and activation |
| ADM-12 | Visible failures | Surface auth, MFA, permission, conflict, indexing, quota, and outage failures honestly |
| ADM-13 | Provider authentication | Require provider identity, MFA, and provider-side roles |
| ADM-14 | Client registry | Let provider roles inspect registered client/deployment metadata |
| ADM-15 | Explicit client operations | Cross-client capabilities require a documented, authorized, audited server operation |
| ADM-16 | Subscription lifecycle | Authorized provider actions update defined plan/status terms and produce an audit record |
| ADM-17 | Suspension/restoration | Provider lifecycle state is enforced by an authoritative backend check, not only UI state |
| ADM-18 | Provider audit | Record actor, target, action, time, outcome, and safe metadata for provider mutations |
| ADM-19 | Trust-domain isolation | Client credentials cannot call provider operations; provider operations are scoped to a registered client |
| ADM-20 | Provisioning | Do not claim automated provisioning until its infrastructure workflow is designed and approved |

ADM-08, ADM-09 lifecycle enforcement, ADM-11, and ADM-13 through ADM-20 depend
on contracts or services that are not established by the current documentation
baseline.

## 9. Knowledge-gap answer behavior

Frequent unanswered questions are a review queue, not a training mechanism:

1. The backend records permitted gap metadata and provides a bounded aggregate.
2. An authorized client admin reviews the gap and authors or approves content.
3. The content follows the tenant knowledge validation, storage, quota, and
   indexing lifecycle.
4. The panel reports pending, indexing, ready, or failed state from an
   authoritative backend result.
5. The answer becomes retrievable only after successful atomic activation.
6. A failed replacement leaves previously active knowledge intact.

Whether an approved answer is stored as a Markdown document or as a dedicated
FAQ record remains undecided. This is not model retraining.

## 10. Product success criteria

- Client users cannot cross tenant boundaries, including through direct API,
  RPC, or Storage calls that bypass UI guards.
- Public widget configuration contains no admin or provider authority.
- Answers and citations are grounded in active eligible tenant knowledge.
- Missing evidence, moderation, service failure, and suspension do not become
  success-shaped responses.
- Indexing retries are idempotent and failed updates preserve active content.
- Provider changes are explicitly authorized and attributable.
- Usage, privacy, retention, recovery, and deployment behavior are measured
  and recorded before production commitments.
