[Auction Journal](../index.md) · [Advertisement](./index.md)

# How do I promote a listing (ticker vs featured national vs featured state)?

All three types use category **Advertise your Listings** and content step **Select** (`SelectAdContent` → published listing).

## Comparison

| Type | `packageFor` | API | Scope | Typical public role |
|------|--------------|-----|-------|---------------------|
| Ticker Symbol | `listing-ticker-ad` | `POST /api/createAdvertisement` (`AdType: listing`) | Site header ticker | `Layouts/Ticker` |
| Featured Listing (National) | `listing-national-ad` | `POST /api/createFeaturedListing` | `isNationalAd: true` | National featured listing blocks |
| Featured Listing (State) | `listing-state-ad` | `POST /api/createFeaturedListing` | `isStateAd: true` + `statesToShowAd` | State-filtered featured listings |

## Prerequisites

- Listing must be **published** and selectable in dashboard.
- Featured listing validators compare ad **end date** to listing **auction date** where implemented.

## Wizard steps (listing category)

1. `AdTypeSelector` — pick ticker or featured package
2. `BuildAd` / `SelectAdContent` — `AdRef` = listing id
3. `SelectDuration` — campaign dates
4. State package only → `SelectState`
5. `createAdProcess` → checkout

## vs “Highlight listing”

[Highlight listing](../listing/highlight-listing.md) (user doc) describes promoting via paid ads in general; this page is the **listing-category** ad types in the Advertisement wizard.

User guide: [`user_side_doc/advertisement/listing-ads.md`](../user_side_doc/advertisement/listing-ads.md)
