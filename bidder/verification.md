[Bidder](./index.md) · [Auction Journal](../index.md)

# Bidder verification (business logic)

End-to-end flow for a **registered bidder** to become a **verified bidder** on Auction Journal: save a **Stripe payment method** (billing + card), upload **government ID** images, then the platform sets **`isVerified: true`** when both checks pass. Public UI: `auctionjournal-public` → `src/app/Components/Bidder/Bidder_Verification` (route `/bidder/verification`). Backend: `updateIdentityVerification`, `verifyBidder`, payment card APIs via `AJStripe`.

---

## Purpose

- Prove the bidder is real (**ID front/back**) and has a **card on file** for auction participation and charges.
- Set platform flag **`isVerified`** (distinct from **`isRegistered`** and email/phone verification at signup).
- Reward trust: **bidder score** event (`verifyingAccount`, +25) and **verified bidder** email on success.

Many auctions default to **`bidderRegistrationType: verifiedBidder`**, so unverified bidders may be blocked from registering until this flow completes.

---

## Preconditions

| Requirement | Field / check |
|-------------|----------------|
| Signed in (JWT) | All routes use `requireAuth` |
| Account registered | `isRegistered: true` (from [Registration](./registration.md)) |
| Not already verified | UI shows manage view when `isVerified: true` |

---

## UI flow (public bidder dashboard)

**Entry:** Sidebar **Verified Bidder** → `/bidder/verification` (`Bidder_Verification/index.jsx` loads profile + payment cards).

| Phase | Component | What happens |
|-------|-----------|--------------|
| **Intro** | `Unverified_Bidder/StartingInfo` | Marketing copy; **Become A Verified Bidder** → form |
| **Step 1** | `VerificationForm` + `PaymentInfoInputs` + `CreatePaymentMethod` | Billing fields + Stripe **CardElement**; save card |
| **Step 2** | `BidderIdentityAdd` | Identity type + front/back upload → identity API |
| **Finalize** | `verifyBidder()` API | Server sets `isVerified` when ID + card both valid |
| **Verified** | `Verified_Bidder` | Manage cards (`PaymentCard`) + `BidderIdentityManage` |

For **application screenshots** (forms and buttons), use [`user_side_doc/bidder/verification.md`](../user_side_doc/bidder/verification.md). Dev docs do not embed UI images.

### Step 1 — Payment (Stripe)

- User taps **+** to open the card form (or sees existing cards if already saved).
- Required billing: name on card, address, city, state, country, postal ZIP (Yup in `Unverified_Bidder`).
- Card data entered in **Stripe Elements** (`@stripe/react-stripe-js`); not posted as raw PAN to AJ backend.
- **Create flow:** `POST /api/payment/method/create` → `stripe.confirmCardSetup` with returned SetupIntent `client_secret` → card attached to bidder’s Stripe customer.
- **List cards:** `POST /api/payment/getCardDetails` (used on page load and after save).
- Button label: **SAVE AND PROCEED TO STEP 2** (or **BECOME VERIFIED BIDDER** if ID already on file — see partial state below).
- Step 2 section stays visually de-emphasized until a card exists (`isCardSaved`).

### Step 2 — Identity document

- Heading: **Driving Licence / State ID** (UI radios: **Driving Licence**, **State ID** only; model also allows **Passport** but this screen does not offer it).
- **Front** and **back** images required for both UI types.
- Client uploads base64 to blob storage (`uploadS3Blob`), then sends **HTTPS URLs** to the API.
- Button: **SAVE DETAILS & BECOME A VERIFIED BIDDER** (disabled until Step 1 card saved).
- On success: `updateBidderIdentity` → then `verifyBidder()` in `BidderIdentityAdd.handleSubmit`.

### Partial completion paths

- **ID on file, no card:** Copy explains ID is already stored; user only completes Step 1, then **BECOME VERIFIED BIDDER** calls `verifyBidder` after card save.
- **Card on file, no ID:** Step 2 enabled after card; identity upload triggers verify at end.
- **Both on file but `isVerified` false:** Submit path still calls `verifyBidder` when user completes the form actions.

### After verification

- Success alert; local storage `user.isVerified` updated.
- **Verified** view: saved card(s) with **UPDATE CARD** / **DELETE CARD**, **+** for another card; ID section with **UPDATE ID**.

---

## Backend: `updateIdentityVerification`

- **Route:** `POST /api/bidder/profile/identity/update` (`updateIdentityVerification.js`).
- **Body:** `identityType`, `imageFrontView`, `imageBackView` (URL strings).
- **Validation:**
  - `imageFrontView` required.
  - If `identityType !== "Passport"`, `imageBackView` required.
  - On failure → `400` `{ msg: "Invalid data", error }` with field errors.
- **On success:** Updates `bidder.identityVerification` with **`isVerified: true`** on the identity subdocument (means “ID images accepted,” not platform verified).
- **Side effects:** Deletes replaced blob URLs; syncs `ClientsOfAuctioneer` `idCardImageFrontView` / `idCardImageBackView` for linked buyer refs.

---

## Backend: `verifyBidder`

- **Route:** `POST /api/bidder/verify` (`verifyBidder.js`).
- **Checks (both required):**
  1. `bidder.identityVerification.isVerified === true`
  2. Stripe customer has **at least one** payment method (`AJStripe.getCardDetails()` → `data.length > 0`)
- **On success:** `Bidder.isVerified = true`, `createBidderScore(verifyingAccount)`, `sendEmail2(verifiedBidderEmailConfig)`.
- **On failure:** `400` `{ msg: "Invalid", isIdentityVerified, isCardVerified }` (flags tell client which part failed).
- **Stripe errors:** Same `400` with flags; card treated as not verified.

---

## Data model (`Bidder`)

```text
identityVerification: {
  identityType: "Driving Licence" | "State ID" | "Passport"
  imageFrontView, imageBackView  // URLs
  isVerified                     // ID docs accepted by updateIdentityVerification
}
isVerified                         // platform verified bidder (verifyBidder)
isRegistered                       // completed signup
stripeCustomer                     // Stripe customer id
```

---

## HTTP surface (reference)

| Action | Method and path |
|--------|-----------------|
| Load bidder profile | `GET /api/bidderProfile` |
| List payment methods | `POST /api/payment/getCardDetails` |
| Create SetupIntent / attach card | `POST /api/payment/method/create` (+ Stripe.js confirm) |
| Update / delete card | `POST /api/payment/card/update`, `DELETE /api/payment/card/delete` |
| Save identity images | `POST /api/bidder/profile/identity/update` |
| Mark platform verified | `POST /api/bidder/verify` |

---

## Client API helpers

`auctionjournal-public/src/lib/api/bidder/profile.js` — `updateBidderIdentity`, `verifyBidder`.  
`src/lib/api/payment/stripe-cards.js` — card CRUD wrappers.

---

## Errors and edge cases (business wording)

- Missing front/back image → identity update rejected.
- Card save failed → Stripe/setup error; Step 2 remains blocked until card exists.
- Verify called without both ID + card → `Invalid` with `isIdentityVerified` / `isCardVerified` hints.
- Corrupt/placeholder ID URL (legacy path ending in `identityVerification-imageFrontView.jpeg`) → UI prompts **reupload** on verified manage screen.

---

## Related topics

- Signup only creates a **registered** bidder: [Registration](./registration.md).
- Whether verification is **mandatory** per auction and **benefits**: [Verification required and benefits](./verification-required.md).
- **Cost** to become a bidder: [Cost](./cost.md) (no platform signup fee).
