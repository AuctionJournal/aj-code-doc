[Auction Journal](../index.md)

# Auction lot fields

Source model: `AJ-Main-Backend/app/models/lots.js` (Mongoose model `lotdetails`).

---

## Identity and placement

| Field | Type | Notes |
|--------|------|--------|
| `Auctioneer` | ObjectId → Auctioneer | Owner auctioneer |
| `auctionDetailId` | ObjectId → auctionDetail | Parent auction |
| `lotNumber` | Number | Unique per auction with `auctionDetailId` |
| `saleOrder` | Number | Catalog / run order |
| `quantity` | Number | Default `1` |

---

## Catalog content

| Field | Type | Notes |
|--------|------|--------|
| `seller` | ObjectId → clientsOfAuctioneer | Consignor / seller client |
| `title` | String | |
| `category` | ObjectId → lotcategory | |
| `categoryDetail` | String | |
| `description` | String | |
| `lotMedia` | Mixed | Images / media payload (schema type `Object`) |
| `suggestedValueMinRange`, `suggestedValueMaxRange` | Number | Estimates |
| `reserve` | Number | |
| `startBid` | Number | Opening level |
| `featured` | Boolean | Default `false` |
| `shipping` | Boolean | Default `false` |

---

## Timing (lot-level)

| Field | Type | Notes |
|--------|------|--------|
| `softCloseMilliSecs`, `bidSoftCloseMilliSecs` | Number | |
| `softCloseSeconds`, `bidSoftCloseSeconds` | String | e.g. `hh:mm:ss` usage in app |
| `closeBidding` | Date | Lot close anchor (aligned with auction rules) |

---

## Bidding state (runtime)

| Field | Type | Notes |
|--------|------|--------|
| `currentBidAmount` | Number | Required, default `0` |
| `nextBidAmount` | Number | Required, default `0` |
| `highestMaximumBidAmount` | Number | Default `0` |
| `lotWinner` | ObjectId → Bidder | |
| `winningFloorBidder` | ObjectId → clientsOfAuctioneer | Onsite / floor context |
| `winnerBidCardNumber` | Number | |
| `bidCount` | Number | Default `0` |
| `viewCount` | Number | Required, default `0` |
| `watchlistCount` | Number | Required, default `0` |

---

## Clerking (onsite / clerk UI)

| Field | Type | Notes |
|--------|------|--------|
| `clerkStatus` | String or null | `Sold`, `Pass`, `Hold`, `High Bid`, or null |
| `isClerkUpdated` | Boolean | Default `false` |
| `clerkUpdatedAt` | Date | |

---

## Fees, taxes, and seller economics

| Field | Type | Notes |
|--------|------|--------|
| `commission` | ObjectId → acMiscFormulas | Often from auction default unless seller-specific rules apply |
| `buyersPermium` | ObjectId → acMiscFormulas | Field name as in schema |
| `buyerTax` | ObjectId → acMiscFormulas | |
| `SellerTax` | ObjectId → acMiscFormulas | |
| `isSellerTaxable` | Boolean | Default `true` |
| `sellerLotExpenses` | Array of subdocs | `account` (ObjectId → AuctioneerMiscAccount), `description`, `amount` |
| `salesPerson` | String | |
| `salesPersoncommission` | String | |
| `buyerLotCharge1`, `buyerLotCharge2` | String | Stored with `ref: "acMiscFormulas"` in schema |
| `defaultsFieldsFromAuction` | Array of strings | Tracks which values were inherited from the auction header |

**Business logic (high level):**

- **Commission / seller tax:** If the seller client uses specific consigner “buy back” style settings, **commission** and **SellerTax** on the lot can follow the client; otherwise the **auction** defaults apply.
- **Buyer premium / buyer tax:** Typically follow **auction** defaults on the lot.
- **isSellerTaxable:** Generally reflects whether the seller is taxable for this lot (e.g. not taxable when the client is tax-exempt under specific consigner settings; otherwise tied to whether the auction collects taxes).

---

## Live webcast linkage

| Field | Type | Notes |
|--------|------|--------|
| `isItLiveWebcastLive` | Boolean | |
| `lotLiveWebcast` | ObjectId → auctionLotLiveWebcast | |
| `auctionLiveWebcast` | ObjectId → auctionLiveWebcast | |

---

## Visibility and lifecycle flags

| Field | Type | Notes |
|--------|------|--------|
| `isHidden` | Boolean | e.g. hide from public catalog until ready |
| `isForQROnly` | Boolean | QR placeholder lot; removed on auction publish (see [build](./build.md)) |
| `isAuctionReady` | Boolean | Default `false`; catalog completeness for publish |
| `isCreatedOnTheFly` | Boolean | Instant / on-the-fly create during event |
| `isLotClosed` | Boolean | |
| `isAuctionClosed` | Boolean | Default `false` |

---

## Indexes (implementation)

- Compound unique: `(auctionDetailId, lotNumber)`.
- Common list filters: `auctionDetailId` with `isHidden`, `closeBidding`, `featured`, `saleOrder`, etc.

---

## Timestamps

- `createdAt`, `updatedAt` (from `timestamps: true`).
