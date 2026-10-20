# Flow Support Chat — Screen Inventory & User Flows

- Version: 0.1 design proposal
- Updated: 2026-10-20
- Status: Proposed screen inventory and flows for review.
- Related: docs 01–07.

## Visual reference

The image below is a concept mockup for the three product surfaces. Labels, sample values, names and statuses are illustrative only; they do not approve business rules or production data.

![Flow Support Chat UI overview](assets/flow-support-ui-overview.svg)

[Open the SVG mockup](assets/flow-support-ui-overview.svg)

## 1. Reading this document

- **Baseline:** needed by the current business requirements.
- **Conditional:** only shown if the feature/policy is approved.
- **OPEN:** must be confirmed; it is not an approved decision.
- Screen IDs provide traceability from design to implementation and QA.

Roles in this document are design categories: Customer, Support Staff, Team Lead/Coordinator, Admin/Ops. Final RBAC and data scopes remain to be confirmed.

## 2. Product map

~~~mermaid
flowchart TD
  A[Flow Support Chat] --> B[Customer Browser Extension]
  A --> C[Service Portal / Support Dashboard]
  A --> D[Admin Portal]
  B --> E[Chat Backend API]
  C --> E
  D --> E
  E --> F[Authorization + Conversation Services]
  F --> G[AI Orchestrator]
  F --> H[Routing + Assignment]
  H --> I[Bitrix24 Adapter]
  F --> J[Worker / Outbox / Audit]
~~~

All surfaces share a design system. Navigation, data and actions are scoped by role, tenant and permission. Hiding a button in the client does not replace server-side authorization.

## 3. A. Browser Extension — Customer UI

| ID | Screen/view | Purpose and content | Primary actions | Required states | Classification |
|---|---|---|---|---|---|
| EXT-01 | Launcher | Open chat; show configured support entry point | Open chat | Default, focus, disabled if policy says so | Baseline |
| EXT-02 | Welcome / identity | Welcome and authentication state | Start or authenticate using approved flow | Loading, authenticated, unauthenticated, expired, error | Baseline; auth flow OPEN |
| EXT-03 | Conversation list | Previous conversations if permitted | Open conversation; start new per policy | Loading, empty, populated, error, denied | Baseline; continue/new policy OPEN |
| EXT-04 | Transcript | Customer, AI and staff messages with timestamps | Read, send | Initial load, older messages loading, new message, reconnecting | Baseline |
| EXT-05 | Composer | Compose and send message | Send, safe retry, attachment if enabled | Empty, typing, sending, accepted/sent, failed, rate limited | Baseline; attachments OPEN |
| EXT-06 | AI response state | Show AI processing and response | Continue conversation | Processing, response, provider unavailable | Baseline |
| EXT-07 | Human handoff | Explain that request is routed to a person | Request human; cancel only if policy allows | Routing, assigned, triage, delayed, error | Baseline |
| EXT-08 | Ticket card | Show permitted Ticket link/status | Open only if server provides valid URL and access permits | Pending create, synced, reconciliation, failed, unavailable | Baseline; mapping OPEN |
| EXT-09 | Customer feedback | Rate support after completion | Submit rating/comment | Available, submitting, submitted, failed | Conditional; feedback policy OPEN |
| EXT-10 | Error/help | Recover from unavailable chat | Retry or use configured help path | Offline, timeout, denied, service unavailable | Baseline |

### Extension layout proposal

- Header: configured product/organization label, minimal connection state, close control.
- Transcript: explicit actor labels, readable message width and restrained metadata.
- Composer: multiline input and obvious send action; show send error next to affected message.
- Handoff/Ticket card: show state separately from Conversation state.
- Footer/legal/help only uses approved content.
- Never show internal notes, internal assignment details or raw integration errors to customers.

## 4. B. Service Portal / Support Dashboard

The Service Portal is a shared workspace for authorized service-team roles. Customer Extension is a separate view, but both share the same design system. Final roles and data scope must follow approved RBAC.

| ID | Screen/view | Purpose and content | Primary actions | Required states | Classification |
|---|---|---|---|---|---|
| SUP-01 | Sign-in/session | Staff authentication | Sign in, retry, re-authenticate | Loading, invalid, expired, denied, success | Baseline; auth method OPEN |
| SUP-02 | Inbox/queue | Authorized queue and conversation list | Search, filter, sort, open | Loading, empty, populated, stale, error | Baseline |
| SUP-03 | Conversation workspace | Transcript, composer and state | Reply, retry, handoff actions per permission | Sending, received, reconnecting, error | Baseline |
| SUP-04 | Customer/context panel | Customer/org context allowed by scope | View permitted details | Loading, partial, unavailable, denied | Baseline |
| SUP-05 | Handoff summary | AI summary, collected details, attempted steps, missing data | Review; edit only if policy permits | Generating, available, failed, stale | Baseline |
| SUP-06 | Assignment/routing | Group, assignee, Primary/Owner/Backup where permitted | Assign/reassign/triage if authorized | Routing, assigned, no mapping, conflict, saving, failed | Baseline |
| SUP-07 | Ticket link/status | External reference and sync status | Open only a valid authorized URL | Pending, synced, reconciliation, action required, failed | Baseline; object mapping OPEN |
| SUP-08 | Public reply composer | Customer-visible response | Reply, attach if enabled | Draft, sending, sent, failed, validation error | Baseline |
| SUP-09 | Internal note composer | Staff-only note | Add note | Draft, saving, saved, failed, denied | Baseline; server-side protection MUST |
| SUP-10 | Activity/audit timeline | Permitted assignment, handoff and integration events | Filter/view details | Loading, empty, populated, partial failure | Baseline by permission |
| SUP-11 | SLA/priority view | SLA/priority only when valid policy/data exists | Sort/filter/escalate if authorized | On track, warning, breached, unknown | Conditional |
| SUP-12 | Search/filters | Search authorized data | Apply/clear filters | Loading, no results, error | Baseline |
| SUP-13 | Notifications | Assignment/reply/action-needed notifications | Open item, mark read if supported | Empty, unread, loading, error | Conditional; channels OPEN |
| SUP-14 | Profile/preferences | Account and allowed preferences | Update permitted preference, sign out | Loading, saving, error | Conditional |
| SUP-15 | No access/no assignment | Explain scope or triage state | Return or authorized next action | Denied, unassigned, mapping missing | Baseline |

### Support workspace layout proposal

Desktop can use three adjustable areas:
1. Queue/list with search, filters, status and unread.
2. Conversation transcript and composer.
3. Context panel for customer, handoff summary, assignment and Ticket link.

Do not force three columns when content becomes too narrow. Tablet/mobile uses list → detail, with context in a separate panel. Important actions must not depend on hover.

## 5. C. Admin Portal

| ID | Screen/view | Purpose and content | Primary actions | Required states | Classification |
|---|---|---|---|---|---|
| ADM-01 | Admin sign-in/access denied | Authenticate and check admin role | Sign in/re-auth | Loading, denied, expired, error | Baseline; auth OPEN |
| ADM-02 | Overview/health | System and integration health from real sources | Open failed jobs/alerts | Loading, partial, healthy, degraded, unavailable | Baseline |
| ADM-03 | User/role management | Manage roles/scopes if approved | Add/update/deactivate mapping | Loading, empty, validation, save, denied | Conditional; identity source OPEN |
| ADM-04 | Customer/org scope | Manage customer/tenant mapping if owned here | View/update mapping | Loading, invalid, conflict, saved | Conditional; source of truth OPEN |
| ADM-05 | Assignment matrix | Customer + group/service → Primary/Owner/Backup/Escalation | Create/edit/validate/activate | Draft, invalid, no recipient, conflict, saved, audited | Baseline |
| ADM-06 | AI provider config | External API or Ollama config without exposing secrets | Test connection, save | Untested, testing, success, failure, secret missing | Capability baseline; provider details OPEN |
| ADM-07 | AI policy/knowledge sources | Manage approved policy and knowledge sources | Edit authorized settings | Draft, invalid, published if approved | Conditional; policy model OPEN |
| ADM-08 | Bitrix24 connection | Connection health and permission status | Test connection; rotate secret via approved mechanism | Not configured, testing, connected, denied, unavailable | Baseline |
| ADM-09 | Ticket mapping | Object, field and status mapping after Bitrix discovery | Validate/publish | Draft, invalid, conflict, active, sync issue | Conditional on Bitrix discovery |
| ADM-10 | SLA/business rules | SLA, priority, auto-close/handoff policies | Edit/publish versioned rules | Draft, invalid, active, conflict | Conditional; policy OPEN |
| ADM-11 | Integration jobs/reconciliation | Queue, retries, unresolved timeouts and action-required jobs | Retry/reconcile with permission | Pending, retrying, succeeded, failed, action required | Baseline ops |
| ADM-12 | Audit log | Who did what, when, permitted before/after changes | Filter/export if allowed | Loading, empty, populated, denied | Baseline |
| ADM-13 | System settings | Approved general settings | Save validated settings | Saving, saved, validation error | Conditional |
| ADM-14 | Error/not found | Generic failure views | Retry/go back | 403, 404, 5xx, offline | Baseline |

## 6. User flows

### FLOW-C01 — Customer sends message and AI responds

~~~mermaid
flowchart TD
  A[Open Extension] --> B{Authenticated?}
  B -- No --> C[Show approved auth flow]
  C --> B
  B -- Yes --> D[Open or create Conversation per policy]
  D --> E[Load transcript]
  E --> F[Customer sends message]
  F --> G[Show sending state]
  G --> H{Server accepted?}
  H -- No --> I[Show safe error and safe retry]
  H -- Yes --> J[Show accepted / pending AI state]
  J --> K{AI result}
  K -- Answer --> L[Show response labelled AI]
  K -- More info --> M[Ask targeted follow-up]
  K -- Handoff --> N[Show handoff state]
~~~

Invariants: do not show “sent” before server confirmation; retry must not duplicate; AI response is labelled.

### FLOW-C02 — Customer requests human support

~~~mermaid
flowchart TD
  A[Customer requests human] --> B[Server validates access and conversation]
  B --> C[Show routing in progress]
  C --> D[Create handoff context]
  D --> E[Apply routing and assignment rules]
  E --> F{Valid recipient?}
  F -- No --> G[Show awaiting coordination]
  F -- Yes --> H[Show assigned / awaiting staff]
  G --> I[Coordinator resolves triage]
  H --> J[Staff replies]
  I --> J
  J --> K[Customer sees public reply]
~~~

If a Ticket must be created/linked, show a separate sync state. An unclear timeout result must show “verification in progress,” not success.

### FLOW-S01 — Staff handles a conversation

~~~mermaid
flowchart TD
  A[Open authorized queue] --> B[Search/filter]
  B --> C[Open conversation]
  C --> D[Read transcript and handoff summary]
  D --> E{Assigned and authorized?}
  E -- No --> F[Show triage/assignment state]
  E -- Yes --> G[Reply or add internal note]
  G --> H{Message type}
  H -- Public reply --> I[Send to customer]
  H -- Internal note --> J[Store as staff-only]
  I --> K[Update state after server confirms]
  J --> L[Show note only to authorized staff]
~~~

Security invariant: internal notes must not be returned by customer APIs or emitted on public channels even if hidden in the frontend.

### FLOW-A01 — Admin edits assignment matrix

1. Open Assignment Matrix; server checks permission.
2. Select customer + group/service scope.
3. Select valid Primary/Owner/Backup/Escalation candidates.
4. Validate mapping, effective period and recipient permissions.
5. Show field errors/conflicts.
6. Review changes before save/activation.
7. Server persists version and audit; UI reports success only after confirmation.
8. Invalid mapping remains unresolved; never silently substitute a staff member.

### FLOW-A02 — Admin investigates Bitrix sync failure

1. Open Integration Jobs and filter action-required failures.
2. Review correlation/request ID, normalized error, attempts and timestamps.
3. If create result is uncertain, reconcile before retry.
4. For permission/validation errors, fix configuration; do not retry forever.
5. Only authorized roles can retry/reconcile.
6. UI reflects confirmed backend/worker status and never exposes credentials or sensitive raw payloads.

## 7. UI state matrix

| State | UI behavior | Appropriate action | Must not do |
|---|---|---|---|
| Initial loading | Skeleton/spinner with context | Wait/cancel if supported | Show fake data |
| Empty | Explain there is no data yet | Create/filter if allowed | Treat empty as error |
| No search results | Explain current filters found nothing | Clear filters | Silently reset filters |
| Sending | Prevent duplicate submit; preserve draft | Wait or safe cancel | Duplicate message |
| Accepted/processing | Show accepted and still processing | Continue within policy | Call it completed |
| Sent/confirmed | Show server-confirmed status | Continue | Confuse with Ticket synced |
| Send failed | Preserve draft where safe; explain retry | Idempotent retry | Lose draft or duplicate |
| AI processing | Label AI activity | Wait or request human if allowed | Imply staff is replying |
| AI unavailable | Safe error and approved next step | Limited retry/handoff per policy | Invent answer |
| Routing | Show routing in progress | Wait | Claim assigned |
| Triage/unassigned | Explain awaiting coordination | Authorized coordinator acts | Assign randomly |
| Assigned | Show assignment only to permitted roles | Staff opens and works | Expose internal metadata to customer |
| Ticket pending | Show creating/syncing | Follow status | Claim Ticket created |
| Ticket reconciliation | Show result being verified | Wait/admin reconcile | Blind create retry |
| Ticket synced | Show confirmed reference/status | Open only authorized valid URL | Assume a deep link format |
| Integration action required | Safe error and admin next step | Authorized admin investigates | Infinite retry |
| Access denied | Safe denial without leaking resource data | Return/re-auth if appropriate | Reveal private resource |
| Session expired | Ask user to re-authenticate | Re-auth | Keep showing private data |
| Offline/reconnecting | Show connection and possible stale data | Safe reconnect/retry | Claim data is current |
| Partial data | Identify failed section | Retry failed section | Discard successful data |
| Saving config | Prevent duplicate submit; retain values | Wait | Report saved early |
| Validation error | Field-level errors and summary where needed | Correct fields | Use color only |
| Unexpected error | Safe message and request ID if useful | Retry only if safe/contact admin | Expose stack trace/secret |

## 8. Navigation model proposal

**Customer Extension:** Chat, previous conversations if allowed, support/Ticket status if available, help/feedback if approved.

**Service Portal:** Inbox/Queue, Search, Conversation Workspace, Triage/Unassigned (authorized roles only), Notifications if enabled, Profile.

**Admin Portal:** Overview/Health, Assignment Matrix, AI provider/policy, Bitrix24 connection/mapping, Integration Jobs/Reconciliation, SLA/Business Rules after policy approval, Users/Roles if managed here, Audit Log, Settings.

Navigation labels and route paths are not final; they depend on frontend framework, RBAC and authentication model.

## 9. Traceability to existing requirements/tests

| Screen/flow | Existing anchor | Additional UI tests |
|---|---|---|
| EXT-02/03 | FR-AUTH, FR-CHAT; TC-001, TC-002 | Loading, expired auth, empty history, denied |
| EXT-04/05 | FR-CHAT; TC-003 | Sending/sent/failed, idempotent retry, reconnect |
| EXT-06/07 | FR-AI/handoff; TC-004–TC-012 | Actor labels, routing vs assigned, triage |
| EXT-08/SUP-07 | Ticket lifecycle; TC-013–TC-016 | Pending/reconciliation/synced; no false success |
| SUP-02/03 | FR-CHAT/AUTH | Empty filters, stale queue, keyboard navigation |
| SUP-05/06 | Routing/assignment; TC-007–TC-012, TC-018 | Missing mapping, unauthorized assignment, conflict |
| SUP-08/09 | Public/internal separation; FR-AUTH-004 | Internal note absent from customer API/channel |
| ADM-05 | Assignment matrix; TC-011, TC-012, TC-018 | Invalid mapping, audit, concurrent update |
| ADM-08/09/11 | Bitrix sync; TC-013–TC-016 | Timeout reconciliation, permission errors, safe retry |
| ADM-12 | Audit/security requirements | Audit filters and permissions |

## 10. Prototype review checklist

- [ ] Customer can distinguish AI and staff.
- [ ] Customer understands message sent vs handoff vs Ticket synced.
- [ ] Staff sees transcript, summary, assignment and Ticket state without losing context.
- [ ] Internal note is distinct from public reply in UI and backend permissions.
- [ ] Admin can identify invalid mappings and jobs needing action.
- [ ] Every data screen has appropriate loading/empty/error/denied states.
- [ ] No action relies on unapproved SLA/status/field assumptions.
- [ ] Extension, desktop, tablet and mobile layouts are reviewed.
- [ ] Keyboard focus, contrast, zoom and screen-reader behavior are tested.
- [ ] Every action maps to an API or is marked pending.

## 11. Open decisions for PO/Tech Lead

| ID | Question | Affected screens |
|---|---|---|
| UX-OPEN-01 | Customer/staff login: SSO, OAuth, local or another flow? | EXT-02, SUP-01, ADM-01 |
| UX-OPEN-02 | When to continue existing Conversation vs create new? | EXT-03 |
| UX-OPEN-03 | Realtime transport and typing/read receipts? | EXT-04, SUP-03 |
| UX-OPEN-04 | Are file attachments in MVP? | EXT-05, SUP-08 |
| UX-OPEN-05 | Which Ticket status/assignee/ID may customer see? | EXT-08 |
| UX-OPEN-06 | Is customer feedback in MVP? | EXT-09 |
| UX-OPEN-07 | May staff reassign, change group or edit AI summary? | SUP-05, SUP-06 |
| UX-OPEN-08 | Does MVP Service Portal include metrics dashboard or only inbox/workspace? | Service navigation |
| UX-OPEN-09 | Does Admin manage users/tenants or consume an external identity source? | ADM-03, ADM-04 |
| UX-OPEN-10 | Final SLA, priority, auto-close and status policy? | SUP-11, ADM-10 |
| UX-OPEN-11 | Bitrix24 object, fields, status mapping and URL format? | SUP-07, ADM-09 |
| UX-OPEN-12 | Notification channels/preferences? | SUP-13 |
| UX-OPEN-13 | Official logo/colors, dark mode, additional languages? | Shared UI |
| UX-OPEN-14 | Final role matrix and permission scopes? | All screens |
