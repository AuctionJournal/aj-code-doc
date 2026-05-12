[Auction Journal](../../index.md)

# Bidding record fields

Source model: `AJ-Main-Backend/app/models/bidding.js` (Mongoose model `Bidding`). One document typically represents **one bidder’s participation on one lot**, with a history array of bid steps.

## Header

| Field | Type | Notes |
|--------|------|--------|
| `bidder` | ObjectId → Bidder | Required, immutable |
| `lot` | ObjectId → lotdetails | Required, immutable |
| `auction` | ObjectId → auctionDetail | |
| `auctioneer` | ObjectId → Auctioneer | |
| `auctioneerClient` | ObjectId → clientsOfAuctioneer | Client row used at bid time |
| `bidding` | Array of subdocs | See below |
| `maxBidAmount` | Number | ≥ 0; summary ceiling where used |
| `acceptanceStatus` | String (enum) | `Accepted` (default), `Pending`, `Declined` |
| `isClerkUpdated` | Boolean | Clerk / auctioneer touch |

**Index:** `(bidder, lot)` for lookup.

## `bidding[]` subdocument

| Field | Type | Notes |
|--------|------|--------|
| `bidAmount` | Number | Required |
| `maxBidAmount` | Number | Used for proxy / max-bid semantics |
| `bidAmountType` | String (enum) | `maximumBidding`, `flatBidding`, `autoBidding`, `clerkingEdit` |

Subdocuments use `timestamps` in schema.

---

## Live / clerk bidding

Clerk-driven or live-webcast bidding may use additional models (e.g. live bid payloads). See [Onsite live webcast bidding](../onsite-livewebcast/bidding.md) and related field notes under [onsite-livewebcast/fields.md](../onsite-livewebcast/fields.md).
