[Auction Journal](../index.md) · [Advertisement](./index.md)

# How do I advertise my blog or video content?

Business category, **select** flow via `SelectAdContent`.

## Types

| Type | `packageFor` | API | Reference |
|------|--------------|-----|-----------|
| Blogs Ad | `blog-ad` | `POST /api/createAdvertisement` (`AdType: blog`) | Existing blog id |
| Video Ad | `video-ad` | `POST /api/createAdvertisement` (`AdType: video`) | Existing video id |

Both use national-style featured fetches on public site (blog/video featured endpoints with national flag).

## Prerequisites

- Blog/video must exist in auctioneer content library and be selectable in dashboard list.
- Content approval rules for blogs/videos still apply to the underlying asset; ad purchase is separate paid placement.

## Flow

1. **Advertise your Business** → Blogs Ad or Video Ad package
2. `SelectAdContent` picks blog or video
3. `SelectDuration` → checkout
4. Go live per [go-live](go-live.md) (admin approval for public display)

User guide: [`user_side_doc/advertisement/blog-video-ads.md`](../user_side_doc/advertisement/blog-video-ads.md)
