[Auction Journal](../index.md)

# Auctioneer Assistants

This module explains the business logic for delegated team access under an auctioneer account in `AJ-Main-Backend`.

## Business Purpose

- Let an auctioneer delegate operational tasks to team members.
- Keep assistants scoped to one auctioneer tenant.
- Support controlled onboarding by invitation instead of open signup.
- Allow auctioneer to disable or remove assistants without deleting primary account ownership.

## Who is a user (assistant) and how they help

In Auction Journal, a "user" in this module means an **auctioneer assistant**: a delegated team account under a specific auctioneer tenant.

Assistants primarily help auctioneers with lot preparation work. A common workflow uses **Lot-AI** ([catalog.auctionjournal.com](https://catalog.auctionjournal.com/)) to:

- open auctions owned by the parent auctioneer
- create and add lots to those auctions
- capture and upload lot images
- organize lot data for auctioneer review and publish readiness

This delegation shortens catalog build time and reduces day-to-day operational load on the primary auctioneer account.

## User onboarding

- [How do I onboard a user?](onboard-user.md)
- [How do I manage existing users and user invitations?](manage-users-and-invitations.md)
- [How does a user self-register from the invitation email sent by an auctioneer?](self-register-from-invite.md)

## Core Lifecycle

1. Auctioneer sends assistant invitation using email.
2. System stores a pending assistant record (`isRegistered = false`) and sends invite link with encrypted JWT token.
3. Assistant completes registration from invite token:
   - sets name + password (+ optional phone)
   - system marks `isRegistered = true`
   - assigns generated assistant `userId`.
4. Assistant can log in only when:
   - invitation exists
   - registration is completed
   - account is not disabled.
5. Auctioneer can edit assistant info, disable/enable access, or delete assistant.

## Key Business Rules

- Assistant record is tenant-bound: each assistant belongs to one `auctioneer`.
- Unique constraint is `(auctioneer, email)`, so same email cannot be duplicated under one auctioneer.
- Invitation token expiry blocks late registration and requires re-invite/resend.
- Disabled assistants are explicitly blocked at login.
- Assistant role can edit own profile fields; auctioneer role controls sensitive updates (phone, password reset, disable flag).

## Invitation and Registration Logic

- Invite flow builds secure token payload with:
  - auctioneer id
  - email
  - role metadata
- Resend flow can skip duplicate DB create and only resend invitation email.
- Registration validates token, finds pending invite record, and prevents:
  - invalid token
  - expired token
  - already-registered invites
  - disabled invite records.

## Login and Access Logic

- Assistant login path is separate from auctioneer/bidder login.
- Successful login returns assistant identity plus parent auctioneer summary.
- Access token and auth cookie are issued similar to other user types.
- Role checks in routes allow:
  - auctioneer-only actions for create/delete/list/invite
  - auctioneer + assistant action for edit endpoint.

## Data Model (Business Meaning)

Primary model: `auctioneerassistant` (`app/models/auctioneer-assistants.js`)

- `auctioneer`: parent owner account (tenant key)
- `userId`: internal assistant id displayed/used in operations
- `firstName`, `lastName`, `email`, `phone`, `profileImage`: assistant identity
- `password`: hashed credential
- `role`: assistant role label
- `isRegistered`: onboarding completion status
- `isDisabled`: operational access kill switch
- `emailOtp`, `emailExpiryTime`: reserved email verification fields

## Operational Notes

- Current fetch endpoint returns all assistants for the auctioneer; request-side name/id filters are not applied in controller yet.
- Direct create flow exists (create assistant with credentials immediately) alongside invite flow; invite flow is preferred for safer onboarding.
- There is no fine-grained permission matrix yet inside this module beyond role gates and disable state.

## Minimal API Map (Reference Only)

- Invite/register
- Create/edit/delete
- Auctioneer assistant list
- Assistant login
