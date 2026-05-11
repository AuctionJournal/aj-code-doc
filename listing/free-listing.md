[Auction Journal](../index.md)

# Free Listing

This page documents free-listing eligibility logic for listing publish flows.

## Business Purpose

- Control whether an auctioneer can publish listings without payment.
- Keep free-listing status managed by a controlled backend integration flow.
- Expose status to listing publish APIs as a hard gate.

## Core Rule

- Listing publish requires `auctioneer.isEligibleForFreeListing = true`.
- If false, publish is blocked and user must complete payment/eligibility flow before listing can go live.

This gate is enforced in:
- `POST /api/auctioneer/listing/draft/publish`
- `POST /api/auctioneer/listing/create`

## Status Source and Update Flow

Free-listing eligibility is updated via dedicated endpoints intended for external/scraper/admin integration:

- `PATCH /api/auctioneer/listing/updateFreeListingStatus`
  - updates single auctioneer by `AuctioneerID`
  - sets:
    - `isEligibleForFreeListing`
    - `websiteWithTag`

- `GET /api/auctioneer/listing/getAuctioneers`
  - returns auctioneers with non-empty `websiteWithTag`
  - includes free-listing status snapshot fields

- `PATCH /api/auctioneer/listing/updateAuctioneersFreeEligibilityStatus`
  - intended bulk update path (currently partial/incomplete in controller)

## Security and Validation Notes

- Free-listing status endpoints require `apikey` header matching `API_KEY_FOR_AJ_SCRAPPER`.
- Single update validates:
  - `auctioneerId` as string
  - `status` in `{true, false, "true", "false"}`
  - `website` as string

## Related Auctioneer Fields

- `isEligibleForFreeListing` (boolean gate)
- `websiteWithTag` (source website/tag context)

Both fields live on `Auctioneer` model and are consumed by listing publish logic.
