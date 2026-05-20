[Auction Journal](../index.md)

# How do I onboard a user?

This page documents the invitation-based onboarding flow for auctioneer users (assistants).

## Business intent

Auctioneer users are onboarded through a controlled invite flow:

1. Auctioneer sends an invite to the user's email from dashboard.
2. User receives a self-registration link.
3. User opens the link in the catalog app and completes account setup.
4. User can then log in as an assistant under that auctioneer tenant.

## Frontend flow

### Invite flow (`auctioneer_dashboard_revamp`)

- UI component: `src/Components/Assistant/InviteAssistant/index.jsx`
- API client: `src/lib/api/assistant/index.js`
- Endpoint called: `POST /api/auctioneer/user/invite`
- Request body:
  - `email` (required, validated in UI as lowercase email)
  - `isResendEmail` (optional, used for resend behavior)

UI behavior:

- Shows modal "Onboard User".
- Validates email format and lowercase before API request.
- On success, shows confirmation that registration link expires in 24 hours.

### Register flow (`lot-ai`)

- Page entry: `src/pages/auth/onboard.jsx`
- Registration form component: `src/components/auth/onboard.jsx`
- API client: `src/lib/api/auth.js`
- Endpoint called: `POST /api/auctioneer/user/register`

Form fields and validation in UI:

- `email` (pre-filled from invite URL query, read-only when present)
- `firstName` (required)
- `lastName` (required)
- `phone` (required, `+1` followed by 10 digits)
- `password` (required, minimum 8 chars with uppercase, lowercase, number, special char)
- `confirmPassword` (must match password)

Request payload sent:

- `firstName`, `lastName`, `phone`, `password`, `token`

## Backend logic (`AJ-Main-Backend`)

### Routes

Defined in `app/routes/auctioneer-assistants.js`:

- `POST /auctioneer/user/invite` (JWT auth + `Auctioneer` role only)
- `POST /auctioneer/user/register` (public route with token verification in controller)

### Invite controller

Controller: `app/controllers/auctioneer-assistants/build.js` → `inviteAuctioneerAssistant`

- Reads auctioneer identity from auth context.
- Requires `email`.
- Generates encrypted JWT token with payload:
  - `auctioneer`
  - `email`
  - `role`
- Creates pending assistant record when not resend (`isRegistered` initially false).
- Sends invite email using `auctioneerAssistantInviteEmailContent`.

Invite link template is built in `app/constant/email-template/emailContentGenerator.js`:

- `${process.env.ASSISTANT_APP}/register?token=...&company=...&email=...`

### Register controller

Controller: `app/controllers/auctioneer-assistants/build.js` → `auctioneerAssistantRegister`

Core checks:

- Requires `token`, `firstName`, `lastName`, `password`.
- Decrypts and verifies JWT token; rejects invalid/expired token.
- Finds pending invite by `auctioneer + email` from token data.
- Rejects when invitation is missing, disabled, or already registered.

On success:

- Hashes password (`bcrypt` with configured rounds).
- Generates numeric `userId`.
- Updates assistant record with profile data and `isRegistered: true`.

## Key operational notes

- Resend invite flow sends email again without creating duplicate record (`isResendEmail`).
- Expired token returns "Request expired! Please contact auctioneer."
- Once registered, same invite cannot be used again ("Already registered.").
