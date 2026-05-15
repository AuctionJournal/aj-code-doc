[Auction Journal](../index.md) · [Payment](index.md)

# Payment cards (Auctioneer & Bidder)

Saved cards let an **Auction Journal customer** (auctioneer or bidder) pay the platform or complete checkout flows using Stripe. Card numbers are collected through **Stripe Elements** on the client; the backend stores a Stripe **Customer** id on the user record (`stripeCustomer` on Auctioneer or Bidder), not raw card data.

**Auctioneer user guide:** [`user_side_doc/auctioneeer/payment-method.md`](../user_side_doc/auctioneeer/payment-method.md)

**Not the same as:** [Stripe Connect](connect-account.md) (auctioneer receives bidder payments).

---

## Business rules (auctioneer)

| Topic | Behavior |
|--------|----------|
| **Purpose** | Pay Auction Journal for platform charges (e.g. **listing** publication, **advertisement** checkout). |
| **Where to manage** | Dashboard **Billings → Payment Method** (`/dashboard/billings/payment-method`). |
| **Stripe customer** | Created on first save if missing; id stored as `Auctioneer.stripeCustomer`. |
| **Minimum cards** | Backend blocks delete when only **one** card remains (`Atleast one card is required`). |
| **Checkout** | Listing/ad flows can send user to `/dashboard/billings/payment/checkout` to pay with saved or new card. |

Bidder card flows use the same APIs with `userType` Bidder; see bidder verification and payment docs when added.

---

## Dashboard UI (auctioneer)

**Codebase:** `auctioneer_dashboard_revamp`

| Item | Value |
|------|--------|
| Page | `Pages/Payments/billinginfo.page.jsx` — heading **Billings** |
| Component | `Components/Payment/BillingInfo/Index.jsx` |
| Nav | Sidebar **Billings → Payment Method** |
| Stripe form | `Components/Payment/StripeCard/` (`CheckoutForm.jsx`) |

### States

1. **No saved cards** — `AddCards`: message and **Add New Card** → `StripeCard` with **Card Details** / **CARD INFORMATION** form.
2. **One or more cards** — `SavedCards`: list with last4, expiry, billing summary; **Update Card** / **Delete Card** per card; **+** tile opens dialog to add another card.

### Save flow (new card)

1. `POST /api/payment/method/create` → SetupIntent `client_secret` (creates Stripe customer if needed).
2. User fills billing fields + Stripe **Card** element.
3. `stripe.confirmCardSetup(client_secret, { payment_method: { card, billing_details } })`.
4. On success, UI refetches `getCardDetails`.

### Update billing (existing card)

- Dialog **CARD INFORMATION** with `isUpdateCard={false}`: updates billing via `POST /api/payment/card/update` only (no new card element).

### Delete

- `DELETE /api/payment/card/delete` with body `{ pmid }` — fails if customer has fewer than two cards.

---

## Backend APIs

**Routes:** `AJ-Main-Backend/app/routes/payment-and-billing.js`  
**Controller:** `app/controllers/paymentAndBilling/cardDetails.js`  
**Stripe helper:** `app/controllers/paymentAndBilling/aj-stripe.js` — class `AJStripe`

| Method | Path | Handler | Purpose |
|--------|------|---------|---------|
| POST | `/api/payment/method/create` | `savePaymentMethod` | Create SetupIntent for customer |
| POST | `/api/payment/getCardDetails` | `getCardDetails` | List card payment methods + default id |
| POST | `/api/payment/card/update` | `updatePaymentMethod` | Update `billing_details` on payment method |
| DELETE | `/api/payment/card/delete` | `deleteStripeCard` | Detach payment method (min 2 cards rule) |
| POST | `/api/payment/method/setDefault` | `setDefaultPaymentMethod` | Set customer default payment method |
| GET | `/api/payment/getStripeDetails` | `getStripeDetails` | Retrieve Stripe customer object |

Frontend clients: `lib/api/payments/stripe.js`, `lib/api/payments/index.js` (`fetchSaveCards`).

---

## Security / PCI

- Card PAN/CVC are entered in **Stripe-hosted Elements** (`CardElement`); AJ backend receives payment method ids and billing metadata only.
- Setup uses Stripe **SetupIntent** for saving cards for off-session or later checkout.
- Publishable key: `REACT_APP_STRIPE_PUBLISHABLE_KEY` on the dashboard.

---

## Related documentation

- [Checkout](checkout.md) — listing/ad payment after card saved
- [Stripe Connect](connect-account.md) — receiving bidder funds
- [Payment index](index.md)
