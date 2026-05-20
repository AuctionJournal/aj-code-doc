[Auction Journal](../index.md) · [Advertisement](./index.md)

# How do I create a print, poster, or banner advertisement?

Business category → **create** flow in `BuildAd.jsx` (not `SelectAdContent`).

## Components and APIs

| Ad type | `packageFor` | Component | Endpoint |
|---------|--------------|-----------|----------|
| National Print | `print-national-ad` | `BuildPrintAd` | `POST /api/createPrintAd` |
| National Poster | `poster-national-ad` | `BuildPosterAd` | `POST /api/createBannerAd` (`adType: poster`) |
| State Poster | `poster-state-ad` | `BuildPosterAd` + state | `POST /api/createBannerAd` |
| National Banner | `banner-national-ad` | `BuildBannerAd` | `POST /api/createBannerAd` (`adType: banner`) |
| State Banner | `banner-state-ad` | `BuildBannerAd` + state | `POST /api/createBannerAd` |

## Typical payload fields

- **Print:** title, images, external link, schedule-related fields, `AdvertisementID` prefix `ADPR_`
- **Poster/Banner:** image upload to blob storage, link URL, `AdvertisementID` prefix `ADPO_` / banner id pattern from component

Uploads use auctioneer blob helpers scoped by `AuctioneerID` + ad id.

## Flow

1. Select **Advertise your Business**
2. Pick print/poster/banner package
3. Complete build form → `onSuccess` → `SelectDuration` → (state if needed) → `createAdProcess` → checkout

Records start `isPublished: false`.

User guide: [`user_side_doc/advertisement/business-create-ads.md`](../user_side_doc/advertisement/business-create-ads.md)
