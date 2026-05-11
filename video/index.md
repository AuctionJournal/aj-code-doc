[Auction Journal](../index.md)

# Video

This module documents the video content feature used by auctioneers in `AJ-Main-Backend`.

## Fields

Primary model: `AdsVideo_db` (`app/models/adsVideo_Db.js`)

1. `auctioneer` - owner reference (`Auctioneer` object id)
2. `VideoID` - generated id (`VideoID_<timestamp>`)
3. `VideoTitle` - title
4. `AuctioneerID` - business id of auctioneer
5. `VideoDescription` - description/content
6. `VideoLink` - required URI link (validated with Joi on create)
7. `PostedBy` - display name of creator
8. `AuthorName` - display name of creator
9. `CoverImage` - optional image path/link
10. `is_published` - publish flag (default `false`)
11. `is_Recommended` - recommendation flag

## Routes and Access

Routes are mounted under `/api` (`app/routes/index.js`).

- `POST /api/createVideo`
  - Auth required: JWT + `Auctioneer` role
  - Body validation: `VideoTitle`, `VideoLink` (URI), `VideoDescription` required
  - Creates video with `is_published = false`
  - File: `app/controllers/ManageVideos/createVideo.js`

- `PUT /api/editVideo`
  - Auth required: JWT + `Auctioneer` role
  - Updates by `req.body.id`
  - Currently updates: `VideoTitle`, `VideoLink`, `PostedBy`
  - File: `app/controllers/ManageVideos/editVideo.js`

- `DELETE /api/deleteVideo`
  - Auth required: JWT + `Auctioneer` role
  - Deletes by `req.body.id`
  - File: `app/controllers/ManageVideos/deleteVideo.js`

- `GET /api/Videos`
  - Public
  - Returns only published and non-recommended videos
  - Filter: `{ is_published: true, is_Recommended: { $ne: true } }`
  - File: `app/controllers/ManageVideos/fetchVideos.js`

- `GET /api/auctioneer/videos`
  - Auth required: JWT + `Auctioneer` role
  - Returns videos for logged-in auctioneer
  - Optional filter supported in code: if `req.body.is_published` then published-only
  - File: `app/controllers/ManageVideos/fetchVideosPostedByUser.js`

- `GET /api/fetchFeaturedVideos`
  - Public
  - Returns active advertisement video refs (`AdType: "video"`, date-range active, `isPublished: true`)
  - Uses cache key `currentVideoAd` with 10-minute TTL
  - File: `app/controllers/ManageVideos/fetchVideos.js`

- `GET /api/recommendedVideo`
  - Public
  - Returns videos where `is_Recommended = true`
  - File: `app/controllers/contact/recommendedVideo.js`

## Behavior Notes

- Video publish/review workflow is implied by `is_published` defaults and read filters.
- No dedicated publish endpoint exists in this module currently; publish state is managed by update flows/admin logic elsewhere.
- `editVideo` and `deleteVideo` currently trust `req.body.id` ownership checks at role level, so API consumers should ensure they only operate on own records.
