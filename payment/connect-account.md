[Auction Journal](../index.md) · [Payment](index.md)

# Stripe Connect (auctioneer)

Stripe Connect lets an auctioneer receive **bidder and settlement payments** into their own Stripe connected account. Auction Journal routes charges to that account; funds are not held on the platform account for those flows.

**User-facing steps:** [`user_side_doc/auctioneeer/stripe-connect.md`](../user_side_doc/auctioneeer/stripe-connect.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Who** | Auctioneers only (stored on `Auctioneer.stripeConnectedAccount`). |
| **Required for auctions** | Yes for most auction types when creating a draft via `auctioneerEligiblityToCreateAuction()`. |
| **“Complete” onboarding** | Stripe account has `details_submitted`, `charges_enabled`, and `payouts_enabled`. |
| **Absentee Bidding** | Eligibility check only requires at least one seller; Stripe Connect is **not** required for that type. |
| **Other auction prerequisites** | Along with Stripe: invoice details, at least one seller (client), and formulas for COMMISSION, BUYER PREMIUM, BUYER TAX, SELLER TAX. See [Auction build](../auction/build.md). |
| **Payment destination** | Bidder charges and settlement captures use `transfer_data.destination` (or equivalent) pointing at the auctioneer’s connected account id. |
| **US accounts** | `createAccount` creates Connect accounts with `country: "US"`. |

---

## Data model

**Auctioneer** (`AJ-Main-Backend/app/models/Auctioneer.js`):

- `stripeConnectedAccount` — Stripe Connect account id (e.g. `acct_…`), set on first account creation.
- `stripeCustomer` — separate Stripe **customer** for paying Auction Journal (listing fees, ads, etc.); not the same as Connect.

Login/session for auctioneers includes `stripeConnectedAccount` in the payload (`authentication/login.js`).

---

## Dashboard UI

**Codebase:** `auctioneer_dashboard_revamp`

| Item | Value |
|------|--------|
| Miscellaneous hub | `/dashboard/miscellaneous` — tile **Stripe Connect** |
| Setup page | `/dashboard/miscellaneous/payment/setup` |
| Page wrapper | `Pages/Miscellaneous/payment.jsx` |
| Root component | `Components/Miscellaneous/StripeConnect/index.jsx` |
| API client | `lib/api/miscellaneous/stripe-connect.js` |

### UI states

1. **Loading** — fetches onboarding status.
2. **Onboarding** (`StripeConnectOnboarding`) — shown when there is no connected account **or** onboarding is incomplete.
   - **Create an account!** → `createStripeConnectAccount()` → `POST /api/stripe/connect/createAccount`.
   - Shows connected account id when present.
   - **Add information** → `createStripeConnectAccountLink(account)` → `POST /api/stripe/connect/linkAccount` → opens Stripe-hosted onboarding in a **new browser tab** (`window.open(url, "_blank")`).
3. **Manage** (`StripeConnectAccount`) — shown when `user.stripeConnectedAccount` exists and `onboarding/status` returns `isCompleted: true`.
   - Toggle **Account Management** / **Payments** (embedded Stripe Connect components).
   - `stripeConnectAccountSession()` → `POST /api/stripe/connect/accountSession` for `client_secret`; initialized via `@stripe/connect-js` and `@stripe/react-connect-js`.

### Auction creation gate (UI)

`Components/Auctions/BuildAuction/InitialCreate/index.jsx` calls draft create; on `400` with `isRequiremenetsMissing`, shows **Requirements Missing** including Stripe Connect when `result.stripeConnectAccount` is false.

---

## Backend APIs

**Routes:** `AJ-Main-Backend/app/routes/payment-and-billing.js`  
**Controller:** `app/controllers/paymentAndBilling/connect-onboarding.js`

| Method | Path | Auth | Purpose |
|--------|------|------|---------|
| POST | `/api/stripe/connect/createAccount` | `requireAuth` | Create Stripe Connect account; save `stripeConnectedAccount` on auctioneer. Returns `{ account }`. Fails with 400 if account already exists. |
| POST | `/api/stripe/connect/linkAccount` | *(none on route)* | Body `{ account }`. Creates Stripe Account Link (`type: account_onboarding`). `return_url` / `refresh_url` use request `origin` and miscellaneous path. Returns `{ url }`. |
| POST | `/api/stripe/connect/onboarding/status` | `requireAuth` | Reads `req.user.stripeConnectedAccount`, retrieves account from Stripe, returns `{ isCompleted, status, account }`. |
| POST | `/api/stripe/connect/accountSession` | `requireAuth` | Creates account session for embedded components; returns `{ client_secret }`. Body includes `embededComponent` from frontend but controller enables payments, account_management, and account_onboarding together. |

### Create account (Stripe)

- Controller: `application` pays fees and losses; `requirement_collection: application`; dashboard type `none`.
- Capabilities requested: `card_payments`, `transfers`.

### Onboarding complete check

```javascript
account.details_submitted && account.charges_enabled && account.payouts_enabled
```

Same logic in `auctioneerEligiblityToCreateAuction()` (`app/controllers/auctioneer/index.js`).

---

## Where Connect is used in payments

| Flow | Location | Notes |
|------|----------|--------|
| Collect from bidder | `paymentAndBilling/payment.js` — `collectPaymentFromBidder` | `paymentIntents.create` with `transfer_data.destination: req.user.stripeConnectedAccount`. |
| Settlement authorize/capture | `auctionOperations/settlement/payment.js` | Uses `auction.Auctioneer.stripeConnectedAccount` when capturing lot winner payments. |

Auctioneer **cards on file** (paying AJ) are documented separately in [cards.md](cards.md) and checkout flows.

---

## Eligibility helper

`auctioneerEligiblityToCreateAuction(auctioneerId, auctionType)` in `app/controllers/auctioneer/index.js`:

- Returns `result` with flags: `invoice`, `sellers`, `commissions`, `buyerPremiums`, `buyerTaxes`, `sellerTaxes`, `stripeConnectAccount`, and `valid`.
- `valid` is true only when all required flags are true (except Absentee Bidding path).

Draft create: `app/controllers/auctionOperations/build-auction.js` — `createAuctionTemplate` returns `400` with `isRequiremenetsMissing: true` and `result` when not valid.

---

## Implementation notes

- Frontend `stripeConnectOnboardingStatus(accountId)` sends `accountId` in the body; backend ignores it and uses `req.user.stripeConnectedAccount`.
- After **Create an account!**, session storage `user` is updated with `stripeConnectedAccount` so the UI can show the account id before reload.
- `linkAccount` is not behind `requireAuth` in routes; callers should still only use account ids belonging to the logged-in auctioneer in production hardening.

---

## Related documentation

- [Payment index](index.md)
- [Auctioneer Miscellaneous](../auctioneer-misc/index.md) — Miscellaneous hub and Stripe tile
- [Auction build](../auction/build.md) — full prerequisite list for draft create
- [Checkout](checkout.md) — platform payments (listings, ads)
