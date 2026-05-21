[Auction Journal](../index.md)

# Auction Building

## Business purpose

An auction is the container for lots, fees, registration, and (by type) timed bidding or live webcast behavior. **Building** is the auctioneer workflow to create that container, refine it as a draft, publish it when ready, and adjust it after publish where the product allows.

Persisted header attributes are documented in **[Auction fields](./fields.md)**.

User-facing: [Create](../user_side_doc/auction/create-auction.md), [Types](../user_side_doc/auction/auction-types.md), [Prerequisites](../user_side_doc/auction/auction-prerequisites.md), [Details](../user_side_doc/auction/build-details.md), [Upload Settings](../user_side_doc/auction/build-upload-settings.md).

---

## Auction types (at create)

| Type | `biddingType` on create | Notes |
|------|-------------------------|--------|
| **Online Timed Auction** | `Online Timed` (model default) | Open/close bidding, soft close |
| **Online Absolute Auction** | `Online Absolute` | Absolute (no reserve) online |
| **Absentee Bidding** | `Absentee Bidding` | Eligibility: sellers only |
| **Onsite With Live Webcast** | `Onsite With Live Webcast` | `lotType` required at create; rings, pre-bid, `biddingTimmings` |

Type and onsite `lotType` are **locked in UI** after draft exists. See [auction types (user)](../user_side_doc/auction/auction-types.md).

---

## Where it lives (auctioneer dashboard)

- **Create:** Auctions list → **+ NEW AUCTION** → **New Auction** modal → draft saved → build screen `/dashboard/auctions/build/:id`.
- **Component tree:** `auctioneer_dashboard_revamp/src/Components/Auctions/BuildAuction/` (`InitialCreate`, `Details`, `UploadSettings`, `Preview`, lots, expenses).
- **Context:** `auctionContext`; field locking: `src/hooks/edit_auction_disable_context.js` (`getCurrentAuctionCondition`, `handleAuctionEditDisable`).

### Build tabs (sections)

| Tab | Subsections (accordions) | Role |
|-----|--------------------------|------|
| **Details** | Auctions Information, Auction Images, New Lot Default, Shipping; **Ring Option** (Onsite + multi-ring) | Identity, media, default lot fees/sellers, shipping · [Rings](./onsite-livewebcast/ring.md) · [User](../../user_side_doc/auction/rings.md) |
| **Upload Settings** | Dates, Notices, Payment / Shipping / Pickup, Bidding, Registration, Bid Increment | Schedule copy, rules, bidding windows, registration email, increments |
| **Preview** | Auction Preview Gallery | Reorder featured gallery images (same auction images) |
| **Lots** | Manage auction lots | See [Auction lot build](../auction-lot/build.md) |
| **Expenses** | Inhouse auction expenses | [Auction expenses](./expenses.md) · [User guide](../user_side_doc/auction/auction-expenses.md) |

**Top actions (draft):** Save Draft, Publish, Delete. **After publish:** Save Draft and Delete hidden; **Save Changes** (replaces Publish) calls `updatePublishedAuction`.

---

## Auctioneer eligibility (before first draft)

Before an auctioneer can create the **first draft**, `auctioneerEligiblityToCreateAuction` runs (`auctioneer/index.js`). Rules **depend on auction type**. User: [Prerequisites](../user_side_doc/auction/auction-prerequisites.md) · [Initial setup](../user_side_doc/auctioneeer/initial-setup.md).

### Absentee Bidding

- At least one **seller** (auctioneer client).

### All other auction types

Online Timed, Online Absolute, and Onsite With Live Webcast require:

- At least one **seller**
- **Stripe Connect** usable (`details_submitted`, `charges_enabled`, `payouts_enabled`)
- At least one **auctioneer invoice** on file
- Saved **formulas**: commission, buyer premium, buyer tax, seller tax

Missing checks → `400` with `isRequiremenetsMissing` flags.

---

## Initial create

**API:** `POST /api/auctioneer/auction/draft/create` → `createAuctionTemplate` in `AJ-Main-Backend/app/controllers/auctionOperations/build-auction.js`.

**Required at create (validated in UI + Joi):**

| Field | Notes |
|-------|--------|
| `AuctionType` | One of four enums; **fixed in UI** after draft exists |
| `Name` | Min 3 characters |
| `startDate` | Today or future (listing date) |
| Indemnification | UI checkbox (not a DB field) |
| `lotType` | **Onsite With Live Webcast only** — Catalogued / Non Catalogued; **fixed in UI** after create |

On create, backend sets defaults (e.g. `bidderRegistrationType: verifiedBidder`; Onsite → ring/pre-bid scaffolding, `bidAmountType` rules).

---

## Start date (listing date)

| Phase | Editable? |
|-------|-----------|
| **Draft / Save Draft** | Yes (auction type + lot type locked in UI only) |
| **Published, now &lt; startDate** | Yes in UI; `updatePublishedAuction` accepts updates |
| **On/after startDate** (registration started) | **Read-only** in UI (`startDate`, `endDate` locked at `REGISTRATION_STARTED+`) |
| **Publish** | If `startDate` is in the past → server sets **now + 5 minutes** (`publishAuctionDetails`) |

**Public visibility:** Auction is not on the public catalog until `startDate`; from then bidders can register ([Registration](./registration.md)).

**Model:** `auctionDetails.startDate` — no schema immutability; **UI phases + publish/delete logic** enforce behavior.

**Delete (published):** Cannot delete after `startDate` when published (registration open). Draft-only delete otherwise ([Delete](#delete)).

---

## Draft

- Auctioneer may **Save Draft** incrementally — **no** “complete all fields” gate on draft (developer-confirmed).
- Only **create modal** fields are strictly required until publish.
- **API:** `POST /api/auctioneer/auction/draft/edit/:id` → `editDraftAuctionDetails` (partial merge; Joi `validate_editDraftAuctionDetails_Request`; invalid keys stripped into `errorLogs`).
- Changing commission/BP/tax/sellers on auction can propagate to lots via `lotsUpdateOnAuctionValuesUpdate` (see lot build doc).

### UI locking (draft)

- `AuctionType` and `lotType` disabled once auction exists (`handleAuctionEditDisable` at `DRAFT`).

---

## Publish

**API:** `POST` publish endpoint → `publishAuctionDetails`; updates use `updatePublishedAuction` when `isPublished`.

**Meaning:**

- Auction is **ready to run** under configured rules.
- **QR-only** lots removed on publish.
- `isPublished` and `Status: Published` only if `publishError.error` is false — response may still be **200 with `error` object** (client shows red field alerts).
- Geo: address geocoded; failure adds `geoLocation` to publish errors.
- Cron, registration bid-permission sync, ring init jobs queued on success.

### Publish validation — all types

From `validate_PublishAuctionDetailsRequest` + `publishAuctionDetails` logic:

- `AuctionID` — min 5, alphanumeric (letter + number), unique
- `Name`, `Description`, `TermsAndCondition`, `ShortBPExplanation`
- `startDate`, `Currency` (USD), USA address fields + successful **geo**
- ≥1 `AuctionImages`
- `ShippingAvailability`
- `reserveType`, `bidAmountType`, `TimeZone`
- Registration email subject/body, bid increments, bidder permission fields

### Publish validation — by auction type

| Type | Additional rules |
|------|------------------|
| **Online Timed / Online Absolute** | `Commission`, `BuyerPremium` required; `OpenBidding` ≥ `startDate`; `closebidding` &gt; open; soft close / bid soft close (`hh:mm:ss`, max 72h) — [Soft close](./soft-close.md); with lots → `endDate` via `updateLotCloseBidding` |
| **Absentee Bidding** | Commission/BP **not** required in Joi |
| **Lots — Catalogued** | ≥1 auction-ready lot; no incomplete non-QR lots |
| **Lots — Non Catalogued** (mainly Onsite) | No lot required; `Commission`, `BuyerPremium`, `sellers`; if `collectTaxes` → buyer/seller tax |
| **Onsite With Live Webcast** | `lotType`, multi-ring flags, `ringOptionAssignBy`, pre-bid / multi-date, `openPreBiddingAt` when pre-bid on, `biddingTimmings`; `OpenBidding` = pre-bid when enabled; single-date → **first timing only**; multi-ring → `auctionRingOptionsValidation`; Non Catalogued → `bidAmountType` forced to **flatBidding** |

---

## Delete

**Product rule:** Draft (unpublished) auctions only in normal use.

**Effects:** Remove auction blob folder, all lots (+ images), expense rows.

**Published:** Server rejects delete when published and **current time &gt; startDate** (registration open).

---

## Edit after publish

**API:** `updatePublishedAuction` (same publish validator; phase-specific client disables).

**Buttons:** Save Draft + Delete **hidden** when `isPublished`; **Save Changes** publishes updates.

**Phase detection:** `getCurrentAuctionCondition` in `edit_auction_disable_context.js` — cumulative disables; early return per phase.

### Online timed / absolute phases

`DRAFT` → `PUBLISHED` (now &lt; startDate) → `REGISTRATION_STARTED` → `BIDDING_OPEN` → `FIVE_MIN_BEFORE_SOFTCLOSE` → `SOFTCLOSE_STARTED` → `AUCTION_CLOSED`

### Onsite with live webcast phases

`REGISTRATION_STARTED` → `PRE_BIDDING_OPEN` (if enabled) → `FIRST_BIDDING_OPEN` → `ONSITE_BIDDING_OPEN` → `AUCTION_CLOSED` — many fields stay editable longer than online soft-close path.

### Locks added by phase (summary)

| Phase | Notable new locks |
|-------|-------------------|
| **PUBLISHED** | `AuctionID`, `currency`, lot `startBid`; onsite also `saleOrder` |
| **REGISTRATION_STARTED** | `startDate`, `endDate`, full address block, `bidAmountType`, `biddingType`, `startingBidcardNumber` |
| **BIDDING_OPEN / PRE_BIDDING_OPEN** | Consolidated seller, handling, reserve, timezone, open bidding, many lot fields |
| **Later onsite / soft-close** | Taxes, formulas, close times, ring options, marketing fields — see full hook file |

Per-field keys: `auctioneer_dashboard_revamp/src/hooks/edit_auction_disable_context.js`.

---

## Implementation reference

| Area | Path |
|------|------|
| Create / edit draft / publish / delete | `AJ-Main-Backend/app/controllers/auctionOperations/build-auction.js` |
| Joi validation | `AJ-Main-Backend/app/controllers/auctionOperations/auction.validation.js` |
| Model | `AJ-Main-Backend/app/models/auctionDetails.js` |
| Dashboard build shell | `auctioneer_dashboard_revamp/src/Components/Auctions/BuildAuction/index.jsx` |
| Draft/publish client validation | `auctioneer_dashboard_revamp/src/lib/functions/auction` |
| Publish readiness UI | `BuildAuction/AuctionBuildProgress/index.jsx` |
| Field disable matrix | `auctioneer_dashboard_revamp/src/hooks/edit_auction_disable_context.js` |

---

## Related

- [Auction lifecycle and stages](./lifecycle.md) · [User: stages](../user_side_doc/auction/auction-stages.md)
- [Auction fields](./fields.md)
- [Registration](./registration.md)
- [Auction lot build](../auction-lot/build.md)
- [Onsite live webcast](./onsite-livewebcast/index.md)
