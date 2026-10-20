# Flow Support Chat — Complete Support Flows and UI Package

- Version: 1.0 candidate baseline
- Updated: 2026-10-20
- Status: Comprehensive draft for Product Owner/Technical review. Production policy still requires approval.
- Scope: Extension, AI response, handoff, Dashboard, Bitrix24, SLA, closure, security, recovery and Admin Portal.

## Visual interface package

### Customer Browser Extension
![Browser Extension screens](assets/flow-support-extension-screens.svg)
[Detailed Extension specification](10-browser-extension-screen-specifications.md)

### Support Dashboard
![Support Dashboard screens](assets/flow-support-dashboard-screens.svg)
[Detailed Support Dashboard specification](11-support-dashboard-screen-specifications.md)

### Admin Portal
![Admin Portal screens](assets/flow-support-admin-screens.svg)
[Admin Portal screen specification](13-admin-portal-screen-specifications.md)

All are vector concept mockups. Sample names, IDs, statuses and metrics are illustrative.

## 1. Master lifecycle

```mermaid
stateDiagram-v2
    [*] --> New
    New --> Accepted: backend persisted message
    Accepted --> AIProcessing
    AIProcessing --> AIResponded: response passes policy checks
    AIProcessing --> AIUnavailable: provider error or timeout
    AIResponded --> AwaitingCustomer: response displayed
    AwaitingCustomer --> AIProcessing: customer follows up
    AwaitingCustomer --> HandoffRequested: asks for human or escalation
    AIUnavailable --> HandoffRequested: fallback chosen
    HandoffRequested --> Routing
    Routing --> Queued: valid team rule
    Routing --> Triage: no mapping or no eligible agent
    Queued --> Assigned: authorized agent accepts
    Triage --> Assigned: authorized lead assigns
    Assigned --> InProgress
    InProgress --> TicketPending: formal tracking required
    TicketPending --> TicketLinked: link verified
    TicketPending --> TicketReconciliation: outcome uncertain
    TicketReconciliation --> TicketLinked: existing or created Ticket verified
    TicketReconciliation --> TicketPending: safe retry after absence verified
    InProgress --> Resolved: resolution criteria met
    Resolved --> Closed: close policy satisfied
    Closed --> Reopened: authorized reopen condition
    Reopened --> Routing
```

## 2. F01 — Intake and persistence

```mermaid
flowchart TD
    A[Customer opens extension] --> B[Restore session or start one]
    B --> C[Enter category and message]
    C --> D{Message valid?}
    D -- No --> E[Show validation; preserve draft]
    E --> C
    D -- Yes --> F[Submit with idempotency key]
    F --> G{Backend accepted and persisted?}
    G -- Yes --> H[Return conversation and message IDs]
    G -- No --> I[Show failed or unknown outcome]
    I --> J[Reconcile by idempotency key before retry]
    J --> F
    H --> K[Enqueue AI processing server-side]
```

Invariant: persist the user message before invoking AI. Do not confirm acceptance before backend acknowledgement. A duplicate idempotency key returns the same logical message.

## 3. F02 — AI First Response

```mermaid
flowchart TD
    A[Accepted customer message] --> B[Load authorized context and approved knowledge]
    B --> C[Call configured provider server-side]
    C --> D{Provider response in time?}
    D -- No --> E[Record safe error and preserve transcript]
    E --> F[Offer retry or human handoff]
    D -- Yes --> G[Apply output and policy checks]
    G --> H{Response allowed and useful?}
    H -- Yes --> I[Display AI response clearly labeled]
    H -- No --> J[Ask clarification or offer handoff]
    I --> K{Customer confirms resolution?}
    K -- Yes --> L[Apply configured resolution rule]
    K -- No --> M[Continue dialogue or handoff]
```

AI must not independently assign staff, claim a Ticket exists, or close a request outside approved policy.

## 4. F03 — Handoff and assignment

```mermaid
flowchart TD
    A[Human requested or escalation triggered] --> B[Persist reason and transcript context]
    B --> C[Evaluate approved assignment matrix]
    C --> D{Valid team and eligible recipient?}
    D -- Yes --> E[Put in configured queue]
    D -- No --> F[Put in Triage or unassigned]
    E --> G[Notify eligible team or agents]
    F --> H[Notify authorized lead]
    G --> I[Agent accepts]
    H --> J[Lead assigns or rule is corrected]
    J --> I
    I --> K[Record owner and audit event]
```

Transfer context includes transcript, customer-visible history, AI summary marked as AI-generated, category suggestion, reason and correlation ID. Staff-only notes never enter customer transcript. Assignment is server-authoritative and audited.

## 5. F04 — Bitrix24 Ticket create/link/update

```mermaid
flowchart TD
    A[Conversation reaches formal tracking condition] --> B{Ticket required by approved policy?}
    B -- No --> C[Continue without Ticket]
    B -- Yes --> D[Check existing conversation-to-Ticket relation]
    D --> E{Existing verified Ticket?}
    E -- Yes --> F[Reuse link and sync permitted fields]
    E -- No --> G[Create or link with idempotency/correlation]
    G --> H{Definitive success response?}
    H -- Yes --> I[Verify ID and relation; persist link]
    H -- No --> J[Mark outcome unknown; reconciliation required]
    J --> K[Query Bitrix24 for matching record]
    K --> L{Existing record found?}
    L -- Yes --> I
    L -- No --> M{Safe retry and absence verified?}
    M -- Yes --> G
    M -- No --> N[Keep pending; operator action]
    I --> O[Expose link only to authorized viewers]
```

Timeout is not proof of failure. Never blindly retry creation after an unknown outcome. The Bitrix24 entity type, fields, deep link and status map must be confirmed before production.

## 6. F05 — SLA and escalation

```mermaid
flowchart TD
    A[Conversation enters SLA-governed state] --> B[Load policy and business calendar]
    B --> C[Start applicable response/resolution clocks]
    C --> D[Evaluate events and pause conditions]
    D --> E{Threshold crossed?}
    E -- No --> D
    E -- Yes --> F[Emit idempotent warning/escalation event]
    F --> G[Notify configured role/channel]
    G --> H{Still overdue after escalation window?}
    H -- Yes --> I[Escalate to next configured level]
    H -- No --> J[Record acknowledgement/resolution]
```

Policy must define timezone, holidays, first-response and resolution clocks, pause/resume conditions, ownership changes, escalation tiers and stop conditions. Do not invent numeric SLA values.

## 7. F06 — Resolution, closure, feedback and reopen

```mermaid
flowchart TD
    A[Agent proposes resolution] --> B[Check required outcome fields]
    B --> C{Requirements met?}
    C -- No --> D[Show missing fields]
    D --> B
    C -- Yes --> E{Customer confirmation required?}
    E -- Yes --> F[Request confirmation]
    F --> G{Confirmed or timeout policy applied?}
    G -- No --> H[Await customer or follow-up state]
    G -- Yes --> I[Apply approved closure rule]
    E -- No --> I
    I --> J[Record outcome and audit]
    J --> K{Feedback eligible?}
    K -- Yes --> L[Request rating/comment]
    K -- No --> M[Closed under policy]
    L --> M
    M --> N{Authorized reopen condition?}
    N -- Yes --> O[Reopen and retain history]
    N -- No --> P[Keep closed]
```

Conversation closure and Bitrix24 Ticket closure are separate unless an approved mapping explicitly links them. Feedback does not change Ticket status by itself.

## 8. F07 — Security, authorization and audit

```mermaid
flowchart TD
    A[Every read/write/action] --> B[Authenticate actor/session]
    B --> C{Identity valid?}
    C -- No --> D[401; no data returned]
    C -- Yes --> E[Authorize role, tenant, team and record scope]
    E --> F{Allowed?}
    F -- No --> G[403; log safe denied event]
    F -- Yes --> H[Validate payload and current state]
    H --> I{Valid?}
    I -- No --> J[Typed validation or conflict error]
    I -- Yes --> K[Perform operation]
    K --> L[Audit sensitive operation]
    L --> M[Return minimum necessary data]
```

Server-side authorization is mandatory. Customer-visible messages and staff-only notes require distinct permissions. Secrets remain server-side and must not be logged. Audit events should capture actor, action, target, timestamp, outcome and correlation ID without unnecessary sensitive payloads.

## 9. F08 — Failure, retry, recovery and reconciliation

```mermaid
flowchart TD
    A[Network/provider/integration operation] --> B{Outcome definitive?}
    B -- Yes success --> C[Persist result and update state]
    B -- Yes failure --> D[Classify retryable or non-retryable]
    D -- Retryable --> E[Bounded backoff retry with jitter]
    D -- Non-retryable --> F[Actionable error and alert]
    B -- Unknown/timeout --> G[Mark pending/unknown; do not claim success]
    G --> H[Reconcile by idempotency/correlation key]
    H --> I{Prior operation found?}
    I -- Yes --> C
    I -- No --> J{Retry safe and allowed?}
    J -- Yes --> E
    J -- No --> F
    E --> A
```

AI, routing, Ticket creation, SLA jobs and reconciliation must run in durable server-side jobs, not depend on the extension remaining awake. Retries are bounded and idempotent; define dead-letter/alerting and operator recovery.

## 10. Cross-flow state models

Conversation: NEW → ACCEPTED → AI_PROCESSING → AI_RESPONDED → AWAITING_CUSTOMER → HANDOFF_REQUESTED → ROUTING → QUEUED/TRIAGE → ASSIGNED → IN_PROGRESS → RESOLVED → CLOSED. Exceptions include AI_UNAVAILABLE, TICKET_PENDING, TICKET_LINKED and TICKET_RECONCILIATION. Reopen only when policy permits.

Ticket linkage: NOT_REQUIRED/NOT_CREATED → PENDING → LINKED_VERIFIED, or PENDING → OUTCOME_UNKNOWN/RECONCILIATION_REQUIRED → LINKED_VERIFIED / SAFE_RETRY_PENDING / OPERATOR_ACTION_REQUIRED. Do not combine conversation and Ticket status in one field.

Message delivery: DRAFT → SENDING → ACCEPTED, or SENDING → FAILED/OUTCOME_UNKNOWN; reconcile uncertain outcomes by idempotency key before retry.

Assignment: UNASSIGNED/TRIAGE → QUEUED → ASSIGNED → TRANSFER_PENDING → ASSIGNED. A conflict preserves authoritative ownership and returns a visible error.

## 11. Screen-to-flow map

| Surface | Screen | Flows |
|---|---|---|
| Extension | Welcome/new conversation | F01 |
| Extension | AI response | F02 |
| Extension | Human handoff | F03, F08 |
| Extension | Live conversation | F01, F03, F08 |
| Extension | Ticket status | F04 |
| Extension | Feedback/resolution | F06 |
| Dashboard | Queue | F03, F05, F08 |
| Dashboard | Agent workspace | F02, F03, F06, F08 |
| Dashboard | Customer/Ticket context | F04 |
| Dashboard | Reconciliation | F04, F08 |
| Admin | Assignment matrix | F03 |
| Admin | SLA policy | F05 |
| Admin | AI/Bitrix24 settings | F02, F04, F08 |
| Admin | Audit and operations | F07, F08 |

## 12. Acceptance and release gates

- [ ] Each screen has role scope, actions, states, API mapping and error behavior.
- [ ] Each flow covers success, negative path, timeout/unknown outcome and recovery.
- [ ] Message send and Ticket create/link are idempotent or reconciled before retry.
- [ ] Customer transcript and staff-only notes are isolated.
- [ ] Routing has a Triage fallback and no random assignment.
- [ ] SLA clocks and escalation thresholds are configuration-driven.
- [ ] Conversation and Ticket lifecycle are distinct and mapped explicitly.
- [ ] Audit covers assignment, sensitive config, Ticket reconciliation and permission changes.
- [ ] Extension suspension does not interrupt critical backend work.
- [ ] API/DB enum and event contracts are reconciled with docs 02, 03, 05 and 06.
- [ ] Security, accessibility, retention, monitoring and recovery tests pass.
- [ ] Product Owner signs off all OPEN decisions before production.

## 13. Decisions blocking production sign-off

1. Bitrix24 entity type, required fields, deduplication key, status map and Ticket visibility.
2. Assignment matrix, eligibility, working hours and no-agent fallback.
3. SLA calendar/timezone, thresholds, pause/resume and escalation tiers.
4. Resolution confirmation, auto-close, reopen window and feedback policy.
5. Identity/SSO, role matrix, tenant boundaries and customer authentication.
6. Attachment limits, malware scanning, retention and privacy notice.
7. Realtime transport, retry budgets, queue technology and alert channels.
8. Supported browsers/extension manifest, languages and accessibility target.
9. Approved brand, labels and production categories.

This is a comprehensive candidate baseline for review, not proof that production policy has been approved.
