[Auction Journal](../index.md)

# Video

This module explains the business logic of auctioneer-created videos used for public content and featured placements.

## Business Purpose

- Allow auctioneers to submit informational/promotional videos.
- Keep submissions in a review-first state before public visibility.
- Support two public display channels:
  - standard public video list
  - recommended/featured placements

## Core Lifecycle

1. Auctioneer creates a video entry.
2. System stores creator identity and assigns a unique business id (`VideoID_<timestamp>`).
3. Video starts as unpublished (`is_published = false`).
4. After separate publish/review handling, it can become visible to public listing.
5. A video may also be marked as recommended (`is_Recommended = true`) for recommendation surfaces.

## Key Business Rules

- Only auctioneers can create, edit, or delete their video entries.
- Public video feed intentionally excludes recommended videos to avoid duplication across sections.
- Recommended section is independently resolved from `is_Recommended = true`.
- Featured video ads are resolved from active advertisement windows (`AdType = "video"` + date range).

## Data Model (Business Meaning)

Primary model: `AdsVideo_db`.

- `auctioneer`, `AuctioneerID`: ownership and tenancy context.
- `VideoID`: business-facing id for lookup/tracking.
- `VideoTitle`, `VideoDescription`, `VideoLink`: content payload.
- `PostedBy`, `AuthorName`: display attribution.
- `CoverImage`: media presentation metadata.
- `is_published`: visibility gate for public listing.
- `is_Recommended`: eligibility for recommendation list.

## Validation and Decisions

- Create flow requires `VideoTitle`, valid URI `VideoLink`, and `VideoDescription`.
- Author/display names are derived from authenticated user profile.
- Public listing query enforces:
  - `is_published = true`
  - `is_Recommended != true`

## Visibility Logic

- **Draft/Review state**: `is_published = false` (not publicly listed).
- **Public list state**: `is_published = true` and not recommended.
- **Recommended state**: `is_Recommended = true` (served in recommended feed).
- **Featured ad state**: comes from Advertisement mapping, not from video flags alone.

## System Notes

- Featured video ads use cache (`currentVideoAd`) with short TTL (10 min) to reduce repeated DB load.
- There is no dedicated publish endpoint in the video route file; publishing appears to be controlled by another admin/review flow.

## Minimal API Map (Reference Only)

- Create/Update/Delete by auctioneer role
- Public list/recommended/featured retrieval
