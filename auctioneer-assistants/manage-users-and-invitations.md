[Auction Journal](../index.md)

# How do I manage existing users and user invitations?

This page documents how auctioneers manage assistant records and pending invitations from the Users list.

## UI location and source

- Frontend module: `auctioneer_dashboard_revamp/src/Components/Assistant`
- Main list screen: `src/Components/Assistant/index.jsx`
- Invite modal: `src/Components/Assistant/InviteAssistant/index.jsx`
- API clients: `src/lib/api/assistant/index.js`

## Supported management actions

From the Users list, auctioneer can:

1. Send invitation email to a new user (`Onboard User`).
2. Resend invitation email for pending invites.
3. Revoke active users (disables access).
4. Revoke sent invitation (shows as invitation canceled).
5. Enable previously disabled/canceled users.
6. Delete user/invitation record.

## Status and action mapping in UI

Status text is derived from `isRegistered` + `isDisabled`:

- `Invitation Sent`: `isRegistered = false`, `isDisabled = false`
- `Invitation Canceled`: `isRegistered = false`, `isDisabled = true`
- `Active`: `isRegistered = true`, `isDisabled = false`
- `Disabled`: `isRegistered = true`, `isDisabled = true`

Action button shown per status:

- Pending invite (`Invitation Sent`) -> `Resend`
- Active -> `Revoke`
- Disabled or invitation canceled -> `Enable`

Additional row actions:

- View/Edit (`Visibility` icon)
- Delete (`Delete` icon)

## API mapping (`AJ-Main-Backend`)

Routes are defined in `app/routes/auctioneer-assistants.js`.

- `POST /api/auctioneer/assistants`
  - Fetch all assistants/invitations for logged-in auctioneer.
- `POST /api/auctioneer/user/invite`
  - Sends invite email and creates pending record (unless resend).
  - `isResendEmail: true` resends invite without creating duplicate record.
- `PATCH /api/auctioneer/assistant/edit`
  - Used for revoke/enable by updating `isDisabled`.
- `DELETE /api/auctioneer/assistant/delete`
  - Permanently deletes assistant/invitation row.

## Backend behavior notes

- Revoke is implemented as disable (`isDisabled = true`), not hard delete.
- Enable restores access/invitation state (`isDisabled = false`).
- Delete removes the record entirely via `findByIdAndDelete`.
- List endpoint currently returns all rows for tenant; request-side filters are not applied in controller.
