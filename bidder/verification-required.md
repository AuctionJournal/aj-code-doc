[Auction Journal](../index.md) · [Bidder](index.md)

# Is verification mandatory? Benefits

Platform **verified bidder** status (`Bidder.isVerified`) is **not required to create a bidder account** ([Registration](./registration.md)). It **is required for many auctions** because auctioneers often set registration type to **Verified Bidder**, and new auctions default that way in the backend.

**User-facing guide:** [`user_side_doc/bidder/verification-required.md`](../user_side_doc/bidder/verification-required.md)  
**How to verify:** [Verification](./verification.md)

---

## Mandatory or optional?

| Level | Rule |
|--------|------|
| **Platform account** | Signup → **registered** bidder only; verification is a separate step. |
| **Per auction** | Auctioneer chooses **Registration type** when building the auction (revamp **Registration** section). |
| **`verifiedBidder`** (default on new auctions) | Bidder must complete [verification](./verification.md) (`isVerified: true`, card + ID) before registering for that auction. Public UI blocks registration and routes to `/bidder/verification`. API: `400` — *"Become verified bidder to register!"* when checks fail. |
| **`registeredBidder`** | Stricter ID path without full verified flow on some checks; bidder still needs **identity document on file** (`identityVerification.isVerified`) for auction registration in public UI. |

**Model default:** `auctionDetails.bidderRegistrationType` defaults to `"verifiedBidder"` (`app/models/auctionDetails.js`). Initial auction create in `build-auction.js` also sets `verifiedBidder`.

**Auctioneer UI labels** (`BuildAuction/Registration`):

- **Registered Bidder**
- **Verified Bidder** — credit card, driving licence/state ID, phone, bidder information

---

## Benefits of becoming a verified bidder

| Benefit | Detail |
|---------|--------|
| **Auction access** | Register for auctions set to **Verified Bidder** (common default). |
| **Faster path to register** | Product copy: *instant registration approvals* and smoother access (`Unverified_Bidder/StartingInfo`). |
| **Trust** | ID + card on file; auctioneers see a stronger participation signal ([Bidder score](../bidder-score/index.md)). |
| **Bidder score** | `verifyBidder` awards **`verifyingAccount`** (+25 in `bidderScoringReasons.verifyingAccountScore`). |
| **Card for registration** | Verified-bidder auction registration sends `paymentMethodID` from saved Stripe methods (`Auction_Registration_Popup`). |
| **Confirmation email** | `verifiedBidderEmailConfig` sent on successful verify. |

Verification is **one-time** per bidder account; card and ID can be updated later on **Verified Bidder** page.

---

## Implementation references

| Area | Location |
|------|----------|
| Auction registration gate | `auctionOperations/registration/index.js` — `createAuctionRegistration` |
| Public registration UX | `auctionjournal-public` — `Auction_Registration_Popup/index.jsx` |
| Verify endpoint | `POST /api/bidder/verify` — requires identity subdoc verified + ≥1 Stripe payment method |
| Auction field | `bidderRegistrationType` — [Auction fields](../auction/fields.md) |

---

## Related

- [Verification](./verification.md) — steps and APIs  
- [Cost](./cost.md) — no platform fee to become a bidder  
- [Registration](./registration.md) — free signup
