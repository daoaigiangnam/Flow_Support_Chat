# Flow Support Chat — UI/UX Implementation & Review Checklist

- Version: 0.1
- Status: Proposed checklist for Product Owner, Designer, Developer and QA.
- Related: docs/07-ui-design-system.md and docs/08-screen-inventory-and-user-flows.md.

## 1. Definition of Ready for a screen

- [ ] Screen ID and user goal are explicit.
- [ ] Allowed roles and data scope are identified.
- [ ] User flow and error paths are described.
- [ ] Relevant loading, empty, populated, error, disabled and denied states are defined.
- [ ] Fields/actions map to an API contract or are marked pending.
- [ ] No unapproved policy is silently assumed; safe fallback is defined.
- [ ] Wireframe/prototype is reviewed by relevant business users.

## 2. Definition of Done for a screen

- [ ] Follows shared design tokens/components.
- [ ] Responsive behavior is tested at target viewport sizes.
- [ ] Keyboard navigation, focus, labels and validation are accessible.
- [ ] Async actions show loading; success appears only after server confirmation.
- [ ] Retry/double-submit does not duplicate retryable actions.
- [ ] No cross-tenant or unauthorized data exposure.
- [ ] Internal notes never appear in customer responses or public notifications.
- [ ] No secrets, raw stack traces or sensitive payloads are exposed.
- [ ] Component/unit and functional tests exist for the chosen stack.
- [ ] Test evidence and PASS/FAIL/defect ID are recorded.
- [ ] GitHub Actions is green before module completion.
- [ ] Business/QA review checks behavior, not visual appearance alone.

## 3. UI test matrix

| ID | Scenario | Expected result |
|---|---|---|
| UI-T01 | Extension opened while logged out | Only approved auth flow; no private history |
| UI-T02 | Send message on slow network | Sending state; safe retry without duplicates |
| UI-T03 | Server accepts message but AI is pending | Show accepted/processing, not resolved |
| UI-T04 | AI provider fails | Safe error and policy-approved next step |
| UI-T05 | Handoff has no valid assignee | Show triage/awaiting coordination |
| UI-T06 | Ticket create times out | Show reconciliation; no false success or blind retry |
| UI-T07 | Staff adds internal note | Clearly labelled and protected from customer API/channel |
| UI-T08 | Staff lacks reassignment permission | Action unavailable; API denies unauthorized call |
| UI-T09 | Invalid assignment mapping | Field error; no silent substitute recipient |
| UI-T10 | Admin save fails validation | Preserve input; show field errors; do not claim saved |
| UI-T11 | Table has no matching rows | No-results state and clear recovery action |
| UI-T12 | Connection lost while viewing queue | Indicate stale data; do not claim realtime freshness |
| UI-T13 | Narrow viewport | Important actions remain available; layout reflows |
| UI-T14 | Keyboard-only use | Visible focus and logical order; dialogs manage focus |
| UI-T15 | 200% zoom | Core content/actions remain usable |
| UI-T16 | Cross-tenant user opens resource URL | No data disclosure; server enforces permissions |
| UI-T17 | API returns partial failure | Preserve successful data; identify failed section |
| UI-T18 | Admin retries/reconciles integration job | UI reflects confirmed backend state |

## 4. Delivery gates

1. **Gate A — Design review:** approve screen inventory, flows, tokens and MVP-impacting open decisions.
2. **Gate B — Contract readiness:** API, auth, role scope and data states are defined; the current API document is a draft, not final OpenAPI.
3. **Gate C — One vertical slice:** implement one end-to-end flow (Extension message → backend → AI/handoff state) after stack confirmation.
4. **Gate D — Tests:** UI, API, permissions, retry/idempotency, responsive and accessibility.
5. **Gate E — CI and review:** finish only when GitHub Actions is green and related acceptance criteria pass.
6. **Gate F — Repeat by module:** expand to Support Portal, Admin and integration states.

## 5. Decisions that must not be assumed

- Official brand guideline/logo.
- Frontend framework and component library.
- Auth/SSO, realtime transport and attachment policy.
- Bitrix24 object type, fields, statuses and deep links.
- SLA, priority, auto-close, business hours, timeout and retry limits.
- Role matrix, data scopes and notification channels.
- Any UI behavior that changes business logic, permissions or data state.

When a decision is open, use a neutral state and mark it OPEN. Never fake data or success to make the design look complete.
