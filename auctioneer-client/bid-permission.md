[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Customer-specific bid permission

**Bid permission** on a customer profile controls how that person’s **auction registrations** are created when they sign up for **your** sales as a linked bidder. It is stored on `accounting.buyer` and applied by `createAuctionRegistration` in `auctionOperations/registration/index.js`.

**User guide:** [Set bid permission for a customer](../user_side_doc/auctioneer-client/bid-permission.md)

---

## Purpose

When a bidder registers for an auction, the backend links or creates a **client** for your auctioneer account, then reads **`client.accounting.buyer.buyerPermissionStatus`** (and related fields) to set the registration’s approval status, spend cap, notes, and decline reason—instead of relying only on the auction’s default `BidderPermission` and bidder score.

This is **per customer, per auctioneer**—the same bidder could have different permission on another auctioneer’s client record.

---

## Dashboard UI

| Item | Detail |
|------|--------|
| Component | `BuildClient/Accounting/BuyerAccounting.jsx` → `BidPermission`, `ChangeBidPermission` |
| Path | Create/edit client step 3 → **Buyer** → **Bid Permission** tab → **Change Permission** |
| Save with client | Values stored in `accounting.buyer` on `POST /api/client/add` or `PATCH /api/client/:refID` |
| View only | `ViewClient/Accounting` displays saved status and notes |

### UI fields (`ChangeBidPermission`)

| `buyerPermissionStatus` | UI label | Extra fields |
|-------------------------|----------|--------------|
| `Default` | Default Permission | — |
| `Approved` | Approved All Bids | `buyerPermission` (dollar cap), `buyerPermissionPrivateNotes`, `buyerPermissionPublicNotes`, `isShareContact` |
| `Declined` | Decline All Bids | `buyerPermissionDeclinedReason` (enum: no_shipping, non_paying, bad_check, chargeback, difficult, no_show, other), same notes + share contact |

---

## Backend — `createAuctionRegistration`

After `createOrUpdateAuctioneerClient(bidder, auctioneerId)` resolves the client:

```text
buyerPermissionStatus === "Approved"
  → bidPermissionStatus = "Permanently Approved"
  → bidderPermission = client.accounting.buyer.buyerPermission
     (unless auction email invitation supplies bidPermission)
  → bidderPermissionFrom = "auctioneerClient" (or "invitationEmail" if invite cap)

buyerPermissionStatus === "Declined"
  → bidPermissionStatus = "Permanently Declined"
  → declineReason = client.accounting.buyer.buyerPermissionDeclinedReason
  → bidderPermission = auction.BidderPermission or invitation cap

buyerPermissionStatus === "Default"
  → bidPermissionStatus = bidder.scoreObtained < 0 ? "Pending" : "Approved"
  → bidderPermission = auction.BidderPermission (or invitation cap)
  → bidderPermissionFrom = "auction" (or "invitationEmail")
```

Always copied onto registration when present on client: `buyerPermissionPrivateNotes`, `buyerPermissionPublicNotes`.

Registration email branch uses resulting `bidPermissionStatus` (pending / declined / approved templates).

---

## Dedicated update API

| Method | Path | Handler |
|--------|------|---------|
| `PATCH` | `/api/auctioneer/client/buyerPermissionUpdate` | `changeClientBidPermission` (`client/buyer.js`) |

Updates client accounting; if client has `buyerRef`, may create bidder score entry on decline and send permanently approved/declined email to the bidder.

Bidder-facing list: `bidderBidPermissions` — clients where `buyerPermissionStatus !== "Default"`.

---

## Related

- [Accounting preferences](./accounting-preferences.md) — buyer block overview  
- [Bidder vs client](./bidder-relationship.md) — auto client on registration  
- [Auction registration](../auction/registration.md) — full registration module
