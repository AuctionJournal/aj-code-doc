[Auction Journal](../index.md)

# How does a user self-register from the invitation email sent by an auctioneer?

This page explains only the self-registration journey that starts after an invitation email is received.

## Flow overview

1. Auctioneer sends an invite from dashboard (`Onboard User`).
2. User receives invitation email with **Join the Team** button.
3. Button opens assistant app registration URL with invite token.
4. User fills onboarding form and submits.
5. Backend verifies token and marks the pending assistant record as registered.

## Email link generation

The invitation email content is generated in:

- `AJ-Main-Backend/app/constant/email-template/emailContentGenerator.js`

Registration URL pattern in email:

- `${process.env.ASSISTANT_APP}/register?token=...&company=...&email=...`

Important points:

- `token` is encrypted JWT and carries invite context.
- `email` is passed in query and used to prefill the form in frontend.

## Frontend self-registration (`lot-ai`)

Files:

- `src/pages/auth/onboard.jsx` (entry page)
- `src/components/auth/onboard.jsx` (form component)
- `src/lib/api/auth.js` (API client)

Behavior:

- Reads `token` and `email` from URL query params.
- Prefills email and keeps it read-only when query email exists.
- User fills:
  - first name
  - last name
  - phone
  - password
  - confirm password
- Submits to `POST /api/auctioneer/user/register`.

## Backend registration handling (`AJ-Main-Backend`)

Route:

- `POST /api/auctioneer/user/register`

Controller:

- `app/controllers/auctioneer-assistants/build.js` → `auctioneerAssistantRegister`

Validation and processing:

- Requires `token`, `firstName`, `lastName`, `password`.
- Decrypts and verifies token.
- Rejects invalid or expired token.
- Finds pending assistant invite using `auctioneer + email` from token.
- Rejects if invite is not found, disabled, or already registered.
- Hashes password and writes profile details.
- Sets `isRegistered: true` and generates assistant `userId`.

## Business outcomes

- Self-registration is invite-only (no open assistant signup).
- One invitation can only complete registration once.
- Expired links require a resend invite from auctioneer dashboard.
