[Auction Journal](../index.md) · [Advertisement](./index.md)

# What types of Advertisement are available to auctioneers?

Auctioneer ads are organized in three dashboard categories (`AdSelector` → `adsList`). Packages and prices come from AJ **Package** records (`packageFor` keys).

## Category 1: Advertise your Listings

| Ad type | `packageFor` (API) | Content step | Public role (summary) |
|---------|-------------------|--------------|------------------------|
| Ticker Symbol | `listing-ticker-ad` | Select published **listing** | Header ticker on public site |
| Featured Listing (National) | `listing-national-ad` | Select listing | Featured block on national listing/home discovery |
| Featured Listing (State) | `listing-state-ad` | Select listing + **state** | Featured block on that state's listing views |

API: `createAdvertisement` (ticker) or `createFeaturedListing` (featured).

## Category 2: Advertise your Auctions

UI shows types (Featured Lot, Top Banner, Ticker) but the category is **`disabled: true`** in dashboard—not available to create today.

## Category 3: Advertise your Business

| Ad type | `packageFor` | Content step | API |
|---------|--------------|--------------|-----|
| National Print Ad | `print-national-ad` | **Create** (BuildPrintAd) | `createPrintAd` |
| National Poster Ad | `poster-national-ad` | **Create** (BuildPosterAd) | `createBannerAd` (`adType: poster`) |
| State Poster Ad | `poster-state-ad` | Create + **state** | `createBannerAd` |
| National Banner Ad | `banner-national-ad` | **Create** (BuildBannerAd) | `createBannerAd` (`adType: banner`) |
| State Banner Ad | `banner-state-ad` | Create + **state** | `createBannerAd` |
| Blogs Ad | `blog-ad` | Select **blog** | `createAdvertisement` (`AdType: blog`) |
| Video Ad | `video-ad` | Select **video** | `createAdvertisement` (`AdType: video`) |

## Select vs create content

| Mode | Component | Used for |
|------|-----------|----------|
| Select | `SelectAdContent` | Listing, blog, video |
| Create | `BuildPrintAd`, `BuildPosterAd`, `BuildBannerAd` | Print, poster, banner |

Routing: `BuildAd.jsx` → `getBuildType(packageFor)`.

## National vs state flag

Featured listing and banner/poster packages carry `behaviour.isNationalAd` / `behaviour.isStateAd` from Package model. State ads require `SelectState` before checkout.

User guide: [National vs state](national-vs-state.md) · [Public placement](public-placement.md)
