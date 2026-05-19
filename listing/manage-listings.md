[Auction Journal](../index.md) · [Listing](./index.md)

# Manage listings and bidding requests

Auctioneers view and act on listings from **Manage Listings** (`/dashboard/listing/manage`). **Bidding requests** (bid passes or listing callbacks) are viewed per listing at `/dashboard/listing/requests`.

**User guide:** [Manage listings](../user_side_doc/listing/manage-listings.md)

---

## Frontend

| Route | Page | Component |
|-------|------|-----------|
| `/dashboard/listing/manage` | `manage.page.jsx` | `ManageListing/index.jsx` |
| `/dashboard/listing/requests` | `biddingRequests.page.jsx` | `BiddingRequests/index.jsx` |
| `/dashboard/listing/edit` | `edit.page.jsx` | `BuildListing` (`isEdit`) |

Nav: **Listings** → **Manage** (`navigationLinks.js`).

### Manage hub

`ManageListing` shows `TypeSelection` with three buckets (company name as heading):

| UI label | API params (`fetchAuctioneerListings`) |
|----------|----------------------------------------|
| Upcoming Auction Listing | `isPublished: true`, `isPastListings: false` → `AuctionDate > now` |
| Past Auctions Listing | `isPublished: true`, `isPastListings: true` → `AuctionDate <= now` |
| Unpublished Auctions Listing | `isPublished: false` (no date filter) |

Pagination: `page` (default 1), `limit` (default 10) → `totalPages`.

### Listing card actions (`ListingCard/index.jsx`)

| State | Buttons |
|-------|---------|
| Published, `AuctionDate` in future | **Edit Auction**, **Bidding Request** |
| Published, past date | **View Auction** (public `/listings/{_id}`), **Bidding Request** |
| Unpublished | **Publish Auction** (`publishListingDraft`), **Edit Auction** |

Publish failure with `isEligibleForFreeListing: false` opens same `PaymentOptions` modal as create flow.

---

## Bidding requests UI

**Entry:** `Link` to `/dashboard/listing/requests` with `state: { listingId, listingType }`.

**Page:** `fetchAuctioneerListingRequests(listingId)` → table + **Download csv**.

**Download:** `downloadAuctioneerListingRequests(listingId, "listing")` → `POST /api/downladBidRequest` with `{ auctionId: listingId }` (CSV blob).

**UI note:** `BiddingRequests` renders a static subtitle `Personal Property & Commercial Real Estate` in `h5` — not wired to listing title; consider fixing to `listing.AuctionTitle`.

---

## API map

| Client (`lib/api/listing/index.js`) | Method | Path | Handler |
|-----------------------------------|--------|------|---------|
| `fetchAuctioneerListings` | POST | `/api/auctioneer/listings` | `fetchAuctioneerListings` |
| `fetchAuctioneerListing` | POST | `/api/auctioneer/listing` | `fetchAuctioneerListing` (`{ id }`) |
| `fetchAuctioneerListingRequests` | POST | `/api/auctioneer/listing/bidder/requests` | `fetchAuctioneerListingRequests` |
| `downloadAuctioneerListingRequests` | POST | `/api/downladBidRequest` | `downladBidRequest` |

Routes: `app/routes/listing.js` (listings), `app/routes/listing-bidder-access.js` (requests, download).

### `fetchAuctioneerListingRequests` (`listing-bidder-access.js`)

- Body: `{ listingId }`.
- Listing must belong to `req.user.id` and `isPublished === true`.
- **OnSite Auction** (`AuctionType` uppercased): `ListingBidPass.find({ listing })`.
- **Else:** `ListingCallbackRequest.find({ listing })`.
- Populates `bidder` (FirstName, LastName, BidderID, Email, Phone) and `listing`.

### Bidder create (public) — reference

| Type | Model | Request number prefix |
|------|--------|------------------------|
| OnSite | `listingBidPass` | `LSPS` + random |
| Other | `listingCallbackRequest` | `LSRQ` + random |

Duplicate create → unique index error (one per bidder per listing).

### CSV export (`downladBidRequest.js`)

Columns: category, auctionType, BidderName, BidderEmail, BidderContact, RequestNumber.

---

## Business rules (current)

- No in-dashboard approve/decline/status workflow for requests — view + export only ([listing index](./index.md)).
- Requests require **published** listing.
- Past vs upcoming only affects date filter and **Edit** vs **View** on card; bidding requests still available for published past listings in UI.

---

## Related

- [Create listing](./create-listing.md)
- [Listing types](../user_side_doc/listing/listing-types.md)
- [Fields](./fields.md)
