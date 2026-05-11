[Auction Journal](../index.md)

# AJ Settings and Info

This module documents platform-level settings and public information endpoints in `AJ-Main-Backend`.

## Business Purpose

- Provide centralized app-level configuration values consumed by other modules.
- Expose public informational content used by web/app experiences.
- Provide URL inventories for public SEO/sitemap style integrations.

## Core Scope

### App Setting Values

- Current persisted app setting includes listing commercial configuration:
  - listing package price (`listingPrice`)
- Listing draft/create flows read this value to set listing price payload when applicable.
- Public package info endpoint exposes listing package details from app settings.

### Public Information Endpoints

- Listing policy
- Auctioneer and bidder FAQ
- About Us
- Data Security
- Privacy Policy
- Terms and Conditions
- General FAQ

These are exposed through `aj-informations` route group and consumed by client apps as static/legal/help content.

### Public App URLs (SEO utility)

- Endpoint generates public URLs by content type:
  - auctions
  - auction lots
  - blogs
  - auctioneers
  - listings
- Used for URL discovery/indexing style use cases.

## Integration Notes

- `listingPrice` value is consumed by listing modules (`listing-draft` flow and package details endpoint).
- App settings are global values, not per-auctioneer overrides in current model.

## Field Reference

- Detailed app-setting model fields: [Fields](./fields.md)

## Minimal API Map (Reference Only)

- `GET/POST` policy/FAQ/info retrieval endpoints via `aj-informations`
- `POST /api/app/urls` for type-based public URL extraction
- `GET /api/fetchListingPackages` for listing package price details
