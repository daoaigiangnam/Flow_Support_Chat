# Flow Support Chat — UI Design System

- Version: 0.1 design proposal
- Updated: 2026-10-20
- Scope: Browser Extension, Support Dashboard / Service Portal, Admin Portal
- Status: Proposed baseline for review, not an approved brand guideline.
- Related: docs 01–06.

## 1. Goals and principles

Three product surfaces share one design system while serving different workflows:
- **Customer Extension:** minimal steps, readable chat, clear status.
- **Support Dashboard / Service Portal:** efficient queue handling, assignment, handoff context, Ticket status and SLA where configured.
- **Admin Portal:** controlled configuration, integration health, audit and operational alerts.

Mandatory principles:
1. Shared tokens and components, but role/tenant-scoped views.
2. Visible states: sending, accepted, AI processing, routing, triage, assigned, Ticket sync.
3. Never claim a Ticket was created/synced until the server confirms it.
4. Distinguish AI, customer, staff and system messages with labels and layout—not color alone.
5. Separate public replies from internal notes; backend authorization is the final boundary.
6. Accessible, keyboard-friendly, responsive layouts.
7. Do not hard-code unapproved SLA, hours, timeouts, priorities, Bitrix status or staff mapping.

## 2. Visual direction

**Proposal:** calm enterprise support UI, neutral surfaces, blue primary actions, teal AI accent, restrained status colors, strong text hierarchy, minimal decoration. These colors are a starting point for review, not official brand colors.

## 3. Color tokens (proposed)

| Token | Hex | Usage |
|---|---|---|
| brand-700 | #1D4ED8 | Primary actions and links |
| brand-800 | #1E40AF | Hover/active |
| brand-50 | #EFF6FF | Selected surface |
| ai-700 | #0F766E | AI label |
| ai-50 | #F0FDFA | AI surface |
| text-primary | #111827 | Main text |
| text-secondary | #4B5563 | Secondary text |
| text-muted | #6B7280 | Low-priority metadata only |
| surface | #FFFFFF | Panels/cards |
| canvas | #F9FAFB | App background |
| border | #E5E7EB | Borders/dividers |
| success-700 | #15803D | Confirmed success |
| success-50 | #F0FDF4 | Success surface |
| warning-700 | #A16207 | Warning/action needed |
| warning-50 | #FEFCE8 | Warning surface |
| danger-700 | #B91C1C | Errors/destructive actions |
| danger-50 | #FEF2F2 | Error surface |
| info-700 | #0369A1 | Informational state |
| info-50 | #F0F9FF | Informational surface |

Rules:
- Status badges always include text; never communicate state with color alone.
- One visually dominant primary action per relevant region.
- Destructive actions use explicit labels and confirmation when appropriate.
- Keep visible focus indicators.
- Dark mode is out of baseline until approved.
- Validate contrast before release: normal text 4.5:1, large text 3:1, controls/focus need adequate contrast.

## 4. Typography

Proposed font: Inter, with system fallbacks (system-ui, -apple-system, BlinkMacSystemFont, Segoe UI, sans-serif). Verify Vietnamese diacritics and font policy before adoption.

| Token | Size / line height | Usage |
|---|---|---|
| display | 28/36 px, 600 | Rare display heading |
| h1 | 24/32 px, 600 | Page title |
| h2 | 20/28 px, 600 | Section title |
| h3 | 16/24 px, 600 | Panel/card title |
| body | 14/22 px, 400 | Dashboard body |
| body-large | 16/24 px, 400 | Extension/chat content |
| label | 13/18 px, 500 | Form labels |
| caption | 12/16 px, 400 | Secondary metadata only |
| code | 12–13 px monospace | Request IDs and error codes |

Use sentence case, avoid all-caps labels, and do not use text smaller than 12 px for business-critical content. Customer chat should prefer 15–16 px and allow browser zoom.

## 5. Spacing and shape

Base spacing scale: 4, 8, 12, 16, 20, 24, 32, 40, 48 px.

| Token | Proposed value |
|---|---:|
| radius-sm | 6 px |
| radius-md | 10 px |
| radius-lg | 14 px |
| control-height-md | 40 px |
| control-height-lg | 44 px |
| icon-sm / md / lg | 16 / 20 / 24 px |

Aim for touch targets of at least 44 × 44 px. Use borders and spacing to group content; reserve shadows for dialogs/popovers.

## 6. Component catalog

### Layout and navigation
- AppShell, SidebarNav, TopBar, Breadcrumb, PageHeader.
- ResponsiveDrawer for narrow viewports.
- SplitPane with list/detail fallback on tablet/mobile.

### Forms and actions
- Button variants: primary, secondary, ghost, destructive; default, hover, focus, disabled, loading.
- TextField, Textarea, Select, Combobox, Checkbox, RadioGroup, Switch.
- FieldLabel, FieldHint, FieldError, SearchInput, FilterBar.
- ConfirmDialog, Toast, InlineAlert, persistent Banner.

### Chat and support
- ConversationListItem: subject/customer, preview, time, status, unread count.
- MessageBubble: actor, timestamp, delivery state.
- MessageComposer: draft, sending, sent/accepted, failed, retry.
- ActorLabel: Customer, AI, Staff, System.
- HandoffBanner and HandoffSummaryPanel.
- AssignmentCard, TicketLinkCard, InternalNoteComposer.
- SlaIndicator only when confirmed policy/data exists.
- AttachmentItem only if attachment policy is approved.

### Data and operations
- StatusBadge, DataTable, Skeleton, EmptyState, ErrorState.
- ConnectionStatus, AuditTimeline, SyncStatusPanel.
- Tables must support loading, empty, error, populated and pagination states where applicable.

## 7. Iconography and motion

Use one consistent icon set (for example Lucide). Icon-only buttons need accessible names. Motion should be functional and brief; respect reduced-motion settings. Do not use animation to conceal latency. Async state must include text, not only a spinner.

## 8. Responsive behavior (proposed breakpoints)

| Token | Viewport | Behavior |
|---|---|---|
| sm | <640 px | One column, drawer navigation, touch-friendly actions |
| md | 640–1023 px | Flexible layout; list/detail instead of split pane |
| lg | 1024–1279 px | Collapsible sidebar, prioritize table columns |
| xl | ≥1280 px | Multi-pane workspace where content remains readable |

**Extension:** browser popup/panel has constrained dimensions. Validate Chrome/Edge and the chosen opening mode; use one primary scroll axis and keep the main CTA visible.

**Support Dashboard:** desktop may use queue + conversation + context. Tablet/mobile should show one primary task at a time and open context separately. Never force three narrow columns.

**Admin Portal:** forms become one column on narrow screens; wide tables use prioritized columns, controlled overflow or card rows. Important actions must not depend on hover.

## 9. Accessibility baseline

Target WCAG 2.2 AA as a design goal, subject to verification:
- Keyboard access, logical focus order, visible focus, no obscured focus.
- Programmatic labels and error associations.
- Status is conveyed by text as well as color.
- Async updates use appropriate live regions without re-reading the entire transcript.
- New chat messages must not unexpectedly move a user away from the message they are reading.
- Dialogs manage focus and restore it when closed.
- Test zoom at 200%, narrow viewport reflow, keyboard and common screen readers.
- Do not automatically focus controls when it would disrupt context.

## 10. Content and microcopy

Use short, direct, non-blaming language. Clearly distinguish:
- “AI is preparing a response”
- “Your request is being routed”
- “Waiting for a support coordinator”
- “Ticket sync result is being verified”
- “Ticket synced” only after confirmed success

Errors should explain what happened, the safe next step, and whether a submitted action was accepted if known. Never expose stack traces, tokens, secrets, internal notes or sensitive raw payloads. Use localization keys; Vietnamese is the baseline language, with additional languages requiring confirmation.

## 11. Token implementation example

This is a token proposal, not a framework decision:

~~~css
:root {
  --color-brand-700: #1D4ED8;
  --color-brand-800: #1E40AF;
  --color-brand-50: #EFF6FF;
  --color-ai-700: #0F766E;
  --color-ai-50: #F0FDFA;
  --color-text-primary: #111827;
  --color-text-secondary: #4B5563;
  --color-surface: #FFFFFF;
  --color-canvas: #F9FAFB;
  --color-border: #E5E7EB;
  --color-success-700: #15803D;
  --color-warning-700: #A16207;
  --color-danger-700: #B91C1C;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --radius-md: 10px;
  --radius-lg: 14px;
  --control-height: 40px;
}
~~~

Do not spread literal hex values through components; consume semantic tokens. Choose framework and token implementation only after frontend stack is confirmed.

## 12. Design review checklist

- [ ] Brand colors/logo confirmed or proposed palette approved.
- [ ] Frontend framework, component library and font policy verified.
- [ ] Relevant components have loading, empty, error, success, disabled states.
- [ ] Message, AI, handoff, triage and Ticket-sync states are distinct.
- [ ] Public messages and internal notes are separate in UI and server authorization.
- [ ] SLA, status mapping and assignment policy are not hard-coded.
- [ ] Extension, dashboard and admin layouts reviewed at target sizes.
- [ ] Keyboard, focus, contrast and zoom checks completed.
- [ ] Every action maps to an approved API or is explicitly marked pending.
- [ ] Business users and support staff review the prototype before production coding.

## 13. Open decisions

| ID | Decision needed | Constraint |
|---|---|---|
| UI-OPEN-01 | Official brand guideline/logo/colors | Palette here is only a proposal |
| UI-OPEN-02 | Frontend framework/component library | Do not assume React/Vue |
| UI-OPEN-03 | Target browsers and extension panel mode | Validate real browser dimensions |
| UI-OPEN-04 | Auth/SSO/session behavior | Login flow is not final |
| UI-OPEN-05 | Polling, SSE or WebSocket | Realtime behavior is not final |
| UI-OPEN-06 | SLA, priority, status mapping | UI consumes approved backend values |
| UI-OPEN-07 | Attachment types, limits and storage | Attachment UI is conditional |
| UI-OPEN-08 | Additional languages and dark mode | Not baseline |
| UI-OPEN-09 | Ticket object, fields and deep links in Bitrix24 | Do not assume mapping |
