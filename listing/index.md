[Auction Journal](../index.md)

# Listing

Listing is an independent module and should be treated separately from Auction workflows.

## Documentation map

| Topic | Dev doc | User doc |
|-------|---------|----------|
| What is a listing / how to create | [Create listing](./create-listing.md) | [Create listing](../user_side_doc/listing/create-listing.md) |
| Manage listings / bidding requests | [Manage listings](./manage-listings.md) | [Manage listings](../user_side_doc/listing/manage-listings.md) |
| Free publish eligibility | [Free Listing Eligibility](../auctioneeer/free-listing-eligibility.md) | [Publish for free](../user_side_doc/listing/free-listing.md) |
| Field reference | [Fields](./fields.md) | — |

## Business Purpose

- Auctioneers create listing records and publish them to public website visibility.
- Listings act as public lead/conversion surfaces for bidder intent before auction participation flows.
- Bidder action path depends on listing type:
  - if `AuctionType` is `ONSITE AUCTION` -> bidder generates bid pass
  - otherwise -> bidder requests callback

## Lifecycle (Current Understanding)

- Draft -> Published -> Live -> Past
- Publish is not time-triggered; it happens when auctioneer explicitly publishes.
- On publish, listing becomes visible on public website.
- Listing moves to Past after `AuctionDate`.

## Draft and Publish Flow (Backend-Aligned)

- Auctioneer can create, edit, fetch, and delete listing drafts.
- Publishing draft (`draft/publish`) converts listing to `isPublished = true`.
- Free-listing eligibility is enforced during publish/create paths.
- Publish path computes geo coordinates from listing address for map-based views.

## Minimum Required to Publish (`/api/auctioneer/listing/draft/publish`)

From backend implementation (`listing-draft.js`), minimum publish requirements are:

- Authenticated auctioneer user
- **Free path:** auctioneer must be free-listing eligible (`isEligibleForFreeListing = true`). **Paid path:** complete listing payment (eligibility not required).
- Draft listing record must exist (`req.body.id`) for draft publish
- Listing must have usable address components for geocoding:
  - `Address1`, `City`, `State`, `Country`, `Zip`

Notes:
- Publish API does not enforce a strict full-field validation checklist in this controller.
- It merges request body with existing draft fields, so many values can come from saved draft.
- If address/geocoding fails, publish fails because geo `location` is required for the saved payload.

## Free vs paid publish

| Path | How listing goes live |
|------|------------------------|
| **Free** | `isEligibleForFreeListing === true` → `POST …/listing/draft/publish` sets `price: 0` and `isPublished: true`. |
| **Paid** | Publish API returns `400` with `isEligibleForFreeListing: false` → dashboard payment checkout → payment webhook sets `isPublished: true` on the listing record. |

Eligibility is set by website tag verification (dashboard + `auction-journal-scapper`) and stored on the auctioneer profile. Recurring checks can revoke eligibility; **already published listings are not unpublished**.

Full flow, APIs, and scrapper integration: [Free Listing Eligibility](../auctioneeer/free-listing-eligibility.md).  
User steps: [Can I publish my listing for free?](../user_side_doc/listing/free-listing.md).

## Bidder Access Intent Flows

- `ONSITE AUCTION` listing -> Generate Bid Pass (`listingBidPass` record)
- Other listing types -> Request A Callback (`listingCallbackRequest` record)
- Business rule: strictly one bidder action record per listing per bidder.
  - One bid pass per bidder per listing
  - One callback request per bidder per listing
- Duplicate attempts are blocked and return user-facing messages.
- No bidder-side cancel/withdraw flow exists yet.

## Auctioneer Handling of Listing Requests

- Current handling is operationally simple:
  - view inbound bidder requests
  - export request data
- No approve/decline/contact-status workflow is implemented yet.

## Post-Publish Edit Policy

- After publish, listing is editable only with limited field scope.
- Full draft-style unrestricted editing is not allowed once listing is public.

## Known Gaps / Pending Clarifications

- Exact list of post-publish editable fields is pending business finalization.
- Whether to enforce a stricter publish checklist (beyond current backend merge behavior) is pending discussion.

## Visibility Notes

- Published flag drives public listing visibility.
- "Live" window starts at publish and lasts until `AuctionDate`.
- After `AuctionDate`, bidder action buttons are disabled.
- Past state is driven by `AuctionDate` crossing current date.

## Building a listing (auctioneer dashboard)

Wizard and publish paths are documented in [Create listing](./create-listing.md) (`BuildListing` component, draft vs paid vs free publish).

## Minimal API Map (Reference Only)

- Auctioneer draft: create/edit/fetch/delete/publish
- Auctioneer direct create/edit listing
- Public listing discovery: listings, featured, map, date filters
- Bidder access actions: create/fetch bid pass, create/fetch callback requests
