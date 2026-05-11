[Auction Journal](../index.md)

# Help And Support

This module is used by `Auctioneer` and `Bidder` users to raise doubts and issues to the AJ support team.

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

### Callback Support (`requestACallBack`)

- User raises a callback request ticket with preferred callback datetime and phone context.
- System creates ticket ID (`RAC_*`) and status `Raised`.
- User can view:
  - single callback request detail
  - full own callback request history
- Callback tickets do not auto-close by callback time; admin decides closure manually.

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

## Minimal API Map (Reference Only)

- Callback:
  - create callback request
  - view single callback request
  - view own callback request history
- Email support:
  - create email support ticket
  - reply to support ticket
  - view single email support ticket
  - view own email support history
