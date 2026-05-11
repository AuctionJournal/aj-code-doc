[Auction Journal](../index.md)

# Blog

This module explains the business logic for auctioneer-authored blogs shown in public content areas.

## Business Purpose

- Enable auctioneers to publish thought leadership, updates, and market content.
- Classify content by `Type` and `Category` for discovery.
- Keep new submissions in a moderation/review stage before public visibility.
- Support featured blog placements through ad scheduling.

## Core Lifecycle

1. Auctioneer submits blog with classification (`Type`, `Category`) and content.
2. System generates a unique `BlogID_<timestamp>`.
3. Blog is saved as unpublished (`is_published = false`).
4. Blog becomes publicly visible only after publish flag is enabled by downstream workflow.
5. Featured display is resolved from active advertisement rules (`AdType = "blog"`), optionally filtered by `Type/Category`.

## Key Business Rules

- Only auctioneers can create blogs.
- Public listing and single-blog views only return published blogs.
- Type/category lists are centrally managed lookup sets (not free-text from public readers).
- Featured blogs are controlled by advertisement windowing, not only by blog publish status.

## Data Model (Business Meaning)

Primary model: `AdsBlog_db`.

- `auctioneer`, `AuctioneerID`: ownership and tenancy context.
- `BlogID`: business identifier.
- `BlogTitle`, `BlogContent`: core content data.
- `Type`, `Category`: classification used by filter/discovery experiences.
- `AuthorName`: display attribution.
- `uploadBlogImage` / `featuredImage`: presentation assets.
- `is_published`: visibility gate for public consumption.

## Validation and Decisions

- Create flow requires: `BlogTitle`, `Type`, `Category`, `BlogContent`, `uploadBlogImage`.
- Public list flow always adds `is_published = true`.
- Single blog flow returns "not found" for unpublished content, even if `BlogID` exists.
- Featured blog query supports optional `Type/Category` match and ignores non-active ad windows.

## Visibility Logic

- **Draft/Review**: `is_published = false`, only owner/internal flows should use it.
- **Public feed**: published-only, optional `Type/Category` filters.
- **Public detail**: published-only by `BlogID`.
- **Featured feed**: ad-driven curated set, not full published list.

## System Notes

- Featured blog results are cached (cache key includes `Type/Category` filter combination) with 10-minute TTL.
- `editBlog` and `deleteBlog` controller implementations exist, but those operations are not currently exposed in the active `blogs` route file.
- Blog type and category dictionaries are exposed as read APIs and act as controlled vocabularies for content tagging.

## Minimal API Map (Reference Only)

- Create by auctioneer role
- Public list/detail by publish status
- Featured retrieval by active ad schedules
