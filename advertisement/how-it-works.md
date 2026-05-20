[Auction Journal](../index.md) · [Advertisement](./index.md)

# How does Advertisement work in Auction Journal? How can an auctioneer benefit from it?

## Business purpose

Advertisement lets auctioneers **pay to promote** their content on the public Auction Journal website. Promoted items get **higher visibility** (featured placement) for a chosen **duration**.

Auctioneers can promote:

- **Listings** (ticker, national featured, state featured)
- **Business presence** (print, poster, banner, blog ad, video ad)
- **Auctions** category exists in UI but is currently **disabled** in dashboard (`Advertise your Auctions`).

## How auctioneers benefit

| Benefit | What it means |
|--------|----------------|
| More visibility | Promoted listings/content appear in featured sections instead of only standard lists |
| Priority placement | National ads surface on broad pages (e.g. home/listing national views); state ads surface on that state's listing/auction views |
| Targeted reach | State-scoped ads let auctioneers focus spend on one market |
| Time-bound campaigns | Packages are priced by duration (days); auctioneer picks start/end window |
| Tied to owned content | Listing ads attach to an existing listing; business ads can link blogs/videos or custom creative |

## End-to-end lifecycle (auctioneer)

```text
Dashboard (/dashboard/advertisement)
  -> Select category (Listings / Business)
  -> Select ad package (type + price/duration)
  -> Build/select ad content
  -> Select run dates (duration calendar)
  -> (State ads) select state
  -> Create ad record (isPublished = false)
  -> Checkout (Billings payment)
  -> Payment success (record updated; may set isPublished in code)
  -> Admin approval (business gate for public display)
  -> Public site fetches published ads in date window
```

**Go live:** User-facing docs state ads go **live after admin approval**. Backend may set `isPublished` on payment via `updateDetailsAfterPayment`; operations may still review before public display. See [go-live](go-live.md).

## Placement rules (national vs state)

| Scope | Typical public behavior (from public API usage) |
|-------|--------------------------------------------------|
| **National** | Featured listings/content requested with `isNationalAd: true` (e.g. home page, national listing views) |
| **State** | Featured content requested with `isStateAd: true` and `statesToShowAd` matching page state filter |

Banner/poster/print ads use the same national/state flags on their models when served to public pages.

## Frontend entry (auctioneer dashboard)

- Route: `/dashboard/advertisement`
- Page: `auctioneer_dashboard_revamp/src/Pages/Advertisement/advertisement.page.jsx`
- Main wizard: `src/Components/Advertisement/AdSelector/index.jsx`

## Backend APIs (create side)

Routes: `AJ-Main-Backend/app/routes/advertisement.js`

| Endpoint | Used for |
|----------|----------|
| `POST /api/createAdvertisement` | Ticker, blog, video ads |
| `POST /api/createFeaturedListing` | National/state featured listing |
| `POST /api/createPrintAd` | National print ad |
| `POST /api/createBannerAd` | Banner and poster (national/state) |

Packages come from `Package` model (`packageFor` keys match dashboard selections). Price and duration limits come from selected package.

## Public consumption (`auctionjournal-public`)

Published ads (date window active + `isPublished: true`) are loaded on marketing pages via helpers such as:

- `fetchFeaturedListings` (national/state)
- `fetchBannerAd` / `currentBannerAd`
- `fetchPrintAd` / `currentPrintAd`
- ticker/blog/video fetches via add-content controllers

Home page national featured listings example: `src/lib/api/page-wise.js` → `homePageapis`.

## Operational constraints (business)

- Ads are **paid**; checkout uses standard auctioneer billing flow (`/dashboard/billings/payment/checkout`).
- Date collision checks prevent overlapping published ads for same reference + scope where implemented.
- Listing ticker/featured ads validate ad end date against listing auction date.

## Related pages

- [Ad types](ad-types.md) · [Create](create-advertisement.md) · [Placement](public-placement.md) · [Go live](go-live.md)

User-facing steps and screenshots: [`user_side_doc/advertisement/how-it-works.md`](../user_side_doc/advertisement/how-it-works.md)
