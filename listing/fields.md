# Listing Fields

Source model: `AJ-Main-Backend/app/models/auctionListing.js`

## Ownership and Identity

- `Auctioneer` (ref `Auctioneer`)
- `ListingID` (unique business id)

Business meaning:
- Ties listing to auctioneer tenant and provides stable external id.

## Lifecycle and State

- `isPublished` (default `false`)
- `AuctionDate`, `AuctionTime`
- `AuctionMonthYear`

Business meaning:
- Draft vs published visibility is controlled by `isPublished`.
- `AuctionDate` is used in live/past timeline behavior.

## Listing Classification

- `AuctionType` (enum):
  - `OnSite Auction`
  - `Live Webcast Auction`
  - `Online Timed Auction`
  - `Online Absolute Auction`
  - `Live Webcast with OnSite Auction`
- `AuctionCategory`
- `CategoryDetails`

Business meaning:
- Drives listing type UX and bidder CTA path (bid pass vs callback request).

## Public Content

- `AuctionTitle`
- `NameOfProduct`
- `ProductDescription`
- `uploadPhoto` (array of media objects)
- `BiddingNotice`
- `AuctionNotice`
- `TermsAndCondition`

Business meaning:
- Public-facing description and policy payload shown on listing detail pages.

## Location and Map Data

- `Address1`, `Address2`, `City`, `State`, `Country`, `Zip`
- `location.type` (required, `Point`)
- `location.coordinates` (required `[longitude, latitude]`)

Business meaning:
- Address supports discovery and map rendering.
- Geo point is mandatory in model and is computed during publish/create flows.

## Commercial and Media Plumbing

- `price`
- `PaymentInfo` (ref `Purchase_order`)
- `imagePathId`

Business meaning:
- Tracks listing pricing/payment linkage and image storage grouping.
