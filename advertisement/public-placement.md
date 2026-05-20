[Auction Journal](../index.md) · [Advertisement](./index.md)

# Where does each advertisement appear on the public website?

**Status:** Minimal reference until product provides a full placement map per ad type. Public consumption is via `auctionjournal-public` API helpers when `isPublished: true` and dates are in range.

## Summary by ad type

| Ad type | `packageFor` | Public consumption (code reference) |
|---------|--------------|-------------------------------------|
| Ticker Symbol | `listing-ticker-ad` | Header ticker (`Layouts/Ticker`) |
| Featured Listing (National) | `listing-national-ad` | Featured listings fetch with `isNationalAd` |
| Featured Listing (State) | `listing-state-ad` | Featured listings fetch with `isStateAd` + state filter |
| National Print | `print-national-ad` | `fetchPrintAd` / home print slot |
| National/State Poster | `poster-*` | `fetchBannerAd` (poster `adType`) |
| National/State Banner | `banner-*` | `fetchBannerAd` (banner `adType`) |
| Blogs Ad | `blog-ad` | Featured blog fetch (national flag) |
| Video Ad | `video-ad` | Featured video fetch (national flag) |

All types are intended for **auctionjournal.com** (public marketing site), not the auctioneer CRM.

## Filters applied on public fetch

Typical query constraints:

- `isPublished: true`
- Current date within `startDate`–`endDate`
- National vs state flags match page context (`isNationalAd`, `isStateAd`, `statesToShowAd`)

Exact page placement (which section, rotation, above-the-fold) may be refined in a future doc update.

User summary: [`user_side_doc/advertisement/public-placement.md`](../user_side_doc/advertisement/public-placement.md)
