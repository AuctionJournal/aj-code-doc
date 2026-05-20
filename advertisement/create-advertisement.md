[Auction Journal](../index.md) · [Advertisement](./index.md)

# How do I create an advertisement?

Wizard lives in `auctioneer_dashboard_revamp/src/Components/Advertisement/AdSelector/index.jsx`, route `/dashboard/advertisement`.

## Step map (`step` state)

| Step | UI | Action |
|------|-----|--------|
| 0 | `TypeSelection` — Select Category | Pick Listings / Business (Auctions disabled) |
| 1 | `AdTypeSelector` | Pick ad type + package (`fetchAJPackages`) |
| 2 | `BuildAd` | Select or create content (`BuildAd.jsx`) |
| — | `SelectDuration` modal | Start/end dates (package `duration` drives end date) |
| — | `SelectState` modal | Required when `package.behaviour.isStateAd` |
| — | Processing dialog | `createAdProcess` → API create |
| — | Checkout | `navigate(/dashboard/billings/payment/checkout)` |

## `createAdProcess` API routing (`lib/api/advertisement/index.js`)

| `packageFor` | Endpoint |
|--------------|----------|
| `listing-ticker-ad`, `blog-ad`, `video-ad` | `POST /api/createAdvertisement` |
| `listing-national-ad`, `listing-state-ad` | `POST /api/createFeaturedListing` |
| `print-national-ad` | `POST /api/createPrintAd` |
| `banner-*`, `poster-*` | `POST /api/createBannerAd` |

Records created with `isPublished: false` until payment confirms.

## Content step routing (`BuildAd.jsx`)

- Listing / blog / video → `SelectAdContent`
- Print → `BuildPrintAd`
- Poster → `BuildPosterAd`
- Banner → `BuildBannerAd`

## Post-create

`handleCheckout` passes product to billing checkout. On payment success, backend may update the ad record (`updateDetailsAfterPayment`). **Public display** is documented as after [admin approval](go-live.md).

See also: [Ad types](ad-types.md), [Go live](go-live.md), [Pricing and duration](pricing-and-duration.md).

User steps: [`user_side_doc/advertisement/create-advertisement.md`](../user_side_doc/advertisement/create-advertisement.md)
