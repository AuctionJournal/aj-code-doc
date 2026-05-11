# AJ settings and info

# Fields

Source model: `AJ-Main-Backend/app/models/appsetting.js`

## App Setting Fields

- `listingPrice` (Number, default `0`)
  - global listing package/base price used in listing-related flows
  - consumed when building/publishing listing drafts and when exposing listing package details

## System Metadata

- `createdAt`, `updatedAt` (timestamps enabled)
- `versionKey` disabled

## Scope Note

- Current model is intentionally small and global.
- Additional app-level configuration fields can be added here as platform settings evolve.
