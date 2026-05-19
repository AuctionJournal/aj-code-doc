[Auction Journal](../index.md)

# Help And Support

This module is used by `Auctioneer` and `Bidder` users to raise doubts and issues to the AJ support team.

**User guides:** [Help and Support](../user_side_doc/help-and-support/index.md)

## Documentation map

| Topic | Dev doc | User doc |
|-------|---------|----------|
| Which channel to use | [Getting help](./getting-help.md) | [Getting help](../user_side_doc/help-and-support/getting-help.md) |
| Request a Callback | [Callback support](./callback-support.md) | [Callback support](../user_side_doc/help-and-support/callback-support.md) |
| Write to Us (email) | [Email support](./email-support.md) | [Email support](../user_side_doc/help-and-support/email-support.md) |
| Live chat widget | [Live chat (Zoho SalesIQ)](./live-chat.md) | [Live chat](../user_side_doc/help-and-support/live-chat.md) |
| Ticket History | [Ticket history](./ticket-history.md) | [Ticket history](../user_side_doc/help-and-support/ticket-history.md) |
| Model fields | [Fields](./fields.md) | — |

## Frontend entry points

### Auctioneer (`auctioneer_dashboard_revamp`)

| Route | Component / page |
|-------|------------------|
| `/dashboard/user-support` | `Components/UserSupport/index.jsx` — **GET IN TOUCH** |
| `/dashboard/user-support/write-to-us` | `WriteToUs` |
| `/dashboard/user-support/ticket-history` | `TicketHistory` |
| `/dashboard/user-support/ticket-history/callback` | `CallbackHistory` |
| `/dashboard/user-support/ticket-history/email` | `EmailHistory` |
| `/dashboard/support/faq` | `Faq` |
| `/dashboard/support/listing-policy` | `ListingPolicy` |

Nav: **Support** submenu in `Components/Layouts/navigationLinks.js`.

API client: `src/lib/api/support/index.js`.

### Bidder (`auctionjournal-public`)

| Route | Component |
|-------|-----------|
| `/bidder/support` | `Bid_Support/index.jsx` |
| `/bidder/support/write-to-us` | `Write-to-us` |
| `/bidder/support/ticket-history` | `Ticekt_History` |
| `/bidder/support/ticket-history/callback/[id]` | `CallBack_History` |
| `/bidder/support/ticket-history/email/[id]` | `Email_History` |

Nav: **Bid Support** in `Bidder_Layout/navData.js`; **FAQs** at `/bidder/faq`.

API: `src/lib/api/support/callback.js`, `email.js`.

## Business Purpose

- Provide authenticated customer support channels for auctioneers and bidders.
- Standardize ticket tracking with a status lifecycle.
- Maintain communication history and attachments for support conversations.
- Trigger user confirmation and admin notification emails when tickets are raised or replied to.

## Support Channels

### Email Support (`writeToUs`)

- User raises support ticket with description, priority, message, and optional attachments.
- System creates ticket ID (`WTUS_*`) and initial status `Raised`.
- Initial user message is appended to `emailThread` with sender type `User`.
- User can add a follow-up reply on the same ticket (`replyEmail`), which:
  - pushes another thread item,
  - sets status to `Open`,
  - can update priority.
- User can view:
  - single email support request detail
  - full own email support history

See [Email support](./email-support.md).

### Callback Support (`requestACallBack`)

- User raises a callback request ticket with preferred callback datetime and phone context.
- System creates ticket ID (`RAC_*`) and status `Raised`.
- User can view:
  - single callback request detail
  - full own callback request history
- Callback tickets do not auto-close by callback time; admin decides closure manually.

See [Callback support](./callback-support.md).

### Live chat (Zoho SalesIQ)

- Global widget on both apps; not stored in ticket models.
- See [Live chat](./live-chat.md).

## Ticket Lifecycle

Both support ticket models use:
- `Raised`: initial state when user creates request
- `Open`: active conversation or follow-up state
- `Closed`: closure state set manually by admin from admin panel

Lifecycle policy:
- Ticket closure is manual and discretionary (admin decision).
- Closed tickets are read-only for users (no further user replies).

## Priority Semantics

- Priority follows `High` / `Medium` / `Low` business severity language.
- Priority indicates expected response urgency from support operations.
- Admin can change priority during ticket handling.

## Ownership and Scope

- Help-and-support APIs are authenticated and scoped to logged-in user (`req.user.id`).
- Ticket creation captures `userType` (`Auctioneer` or `Bidder`) for support segmentation.
- Customer-side visibility is owner-only: users can access only their own tickets.
- Admin-side moderation actions (status change/comments/replies from admin console) are handled in admin backend flows.

## Notifications and Communication

- On ticket creation:
  - success email is sent to user
  - notification email is sent to support/admin pipeline
- On email-support reply:
  - notification is sent again with latest message context.

## Data Models (Business Meaning)

- `writeToUs`
  - email-based support thread with `emailThread`, priority, attachments, and status.
- `requestACallBack`
  - callback request ticket with callback schedule, contact details, optional comments, and status.
- Detailed model field reference: [Fields](./fields.md)

## API map (customer support)

Mounted at `/api/` via `app/routes/helpAndSupport.js` (JWT on all).

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/api/customer/support/callbackRequest` | Create callback |
| POST | `/api/customer/support/view/callbackRequests` | Callback history |
| POST | `/api/customer/support/view/callbackrequest` | Single callback |
| POST | `/api/customer/support/emailRequest` | Create email ticket |
| POST | `/api/customer/support/replyEmail` | Reply on email ticket |
| POST | `/api/customer/support/view/emailRequests` | Email history |
| POST | `/api/customer/support/view/emailrequest` | Single email ticket |
