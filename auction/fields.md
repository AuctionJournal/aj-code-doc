[Auction Journal](../index.md)

# Auction fields

Source model: `AJ-Main-Backend/app/models/auctionDetails.js` (Mongoose model `auctionDetail`).

Persisted **auction header** fields. Lots, live webcast sessions, and other children use separate models.

---

## Identity and ownership

| Field | Type | Notes |
|--------|------|--------|
| `Auctioneer` | ObjectId → Auctioneer | Owner |
| `AuctionType` | String (enum) | `Online Timed Auction`, `Online Absolute Auction`, `Absentee Bidding`, `Onsite With Live Webcast` |
| `AuctionID` | String | Unique when set; sparse unique index |
| `Name` | String | |
| `Description` | Mixed | Schema type `Object` |
| `Status` | String (enum) | `Draft`, `Published`, `Registration Open`, `Live`, `Closed` |
| `isPublished` | Boolean | Default `false` |
| `isSettlementGenerated` | Boolean | |

---

## Schedule and bidding windows

| Field | Type | Notes |
|--------|------|--------|
| `startDate` | Date | Public visibility / registration timing per product rules |
| `endDate` | Date | Auction end; updated with type-specific logic |
| `OpenBidding` | Date | Online bidding open |
| `closebidding` | Date | Timed close anchor |
| `TimeZone` | String (enum or null) | `Pacific/Honolulu`, `America/Juneau`, `America/Los_Angeles`, `America/Boise`, `America/Chicago`, `America/Detroit` |
| `isPreBiddingAllowed` | Boolean | Onsite live webcast |
| `isMultiDateBidding` | Boolean | Onsite live webcast |
| `openPreBiddingAt` | Date | If pre-bidding enabled |
| `biddingTimmings` | Array | Subdocs: `startAt` (Date), `isClosed` (Boolean, default false) |

---

## Catalog mode and counts

| Field | Type | Notes |
|--------|------|--------|
| `lotType` | String (enum) | `Catalogued`, `Non Catalogued` |
| `totalLots` | Number | Default `0` |
| `incompleteLots` | Array of numbers | |
| `isAnalysingLots` | Boolean | |

---

## Location

| Field | Type | Notes |
|--------|------|--------|
| `Address1`, `Address2` | String | |
| `City`, `State`, `Country` | String | |
| `Zip` | Number | |
| `location` | GeoJSON-like | `type: Point`, `coordinates: [lng, lat]` |

---

## Fees, taxes, sellers

| Field | Type | Notes |
|--------|------|--------|
| `Currency` | String | Enum `USD` |
| `collectTaxes` | Boolean | Default `false` |
| `Commission` | ObjectId → acMiscFormulas | |
| `BuyerPremium` | ObjectId → acMiscFormulas | |
| `BuyerTax` | ObjectId → acMiscFormulas | |
| `SellerTax` | ObjectId → acMiscFormulas | |
| `lotReserveAmount` | Number | Default `0` |
| `defaultSeller` | ObjectId → clientsOfAuctioneer | |
| `sellers` | ObjectId[] → clientsOfAuctioneer | Default `[]` |

---

## Shipping

| Field | Type | Notes |
|--------|------|--------|
| `ShippingAvailability` | String (enum) | `yes`, `no`, `specific`; default `no` |
| `ShippingAccount` | String | |

---

## Onsite live webcast — rings

| Field | Type | Notes |
|--------|------|--------|
| `isMultipleRingAuction` | Boolean | |
| `ringOptionAssignBy` | String (enum) | `range by lot number`, `range by sale order` |
| `ringOptions` | Array | `rangeBegin`, `rangeEnd`, `lotCount`, `lotsLeft`, `liveWebcast` (ObjectId), `isRingLive`, `isRingPaused`, `isClosed`, `isCompletelyClosed` |

---

## Bidding configuration

| Field | Type | Notes |
|--------|------|--------|
| `biddingType` | String (enum) | `Online Timed`, `Online Absolute`, `Live Webcast`, `Onsite`, `Onsite With Live Webcast`, `Absentee Bidding` |
| `reserveType` | String (enum) | `publicBidding`, `sealedBidding` |
| `bidAmountType` | String (enum) | `maximumBidding`, `flatBidding` |
| `softCloseMilliSecs`, `bidSoftCloseMilliSecs` | Number | |
| `softCloseSeconds`, `bidSoftCloseSeconds` | String | App uses `hh:mm:ss` style |
| `autoSyncGroupedLotSoftCloseTime` | Boolean | |
| `showImmediateBidStatusOnClosedLots` | Boolean | |
| `biddingIsTimeTheMoneyOnLotsWithAQuanityGreaterThanOne` | Boolean | |
| `bidIncrement` | Array | `{ amount, increment }` subdocs without `_id` |
| `bidIncrementType` | String (enum) | `Force Min Bid to Bid Increment Schedule`, `Apply Bid Increments by Each` |

---

## Bidder registration defaults

| Field | Type | Notes |
|--------|------|--------|
| `bidderRegistrationType` | String (enum) | `registeredBidder`, `verifiedBidder` (default `verifiedBidder`) |
| `startingBidcardNumber` | Number | Default `1` |
| `BidderPermission` | Number | |
| `emailSubject`, `emailBody` | String | |
| `requiredLastCreditCardAuthorizationInDays` | String | |

---

## Copy, notices, and images

| Field | Type | Notes |
|--------|------|--------|
| `TermsAndCondition` | String | |
| `ShortBPExplanation` | String | |
| `AuctionImages` | Mixed | Schema type `Object` |
| `previewDateTime`, `auctionDateTime`, `checkoutDateTime` | String | |
| `biddingNotice`, `auctionNotice` | Mixed | |
| `paymentInformation` | String | |
| `shippingPickUp` | String | |
| `AccounType` | String | Field name as in schema |

---

## Implementation notes (model file)

- **Indexes:** `isPublished`, `Auctioneer`, `State`, `AuctionType`, compounds for listing, `location` (2dsphere), `createdAt`.
- **Cache:** After save / findOneAndUpdate / findOneAndDelete, Redis key `auction:<id>` is deleted.

## Pending / commented in schema source

Fields that are **commented out** in `auctionDetails.js` (not part of the live schema) include `UseConslidatedSeller`, `AddHandlingCharge`, `HandlingChargeType`, `FixedAmount`, and older duplicate `softCloseSeconds` / `bidSoftCloseSeconds` comment blocks. The schema file also lists `totalLots` twice in source; runtime uses a single definition.
