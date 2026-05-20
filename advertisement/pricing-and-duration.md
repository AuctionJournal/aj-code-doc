[Auction Journal](../index.md) · [Advertisement](./index.md)

# How does advertisement pricing and duration work?

## Package source

Dashboard loads packages with `fetchAJPackages({ packageFors })` filtered by category (`AdSelector` → `adsList.packageFors`). Each row is a `Package` document:

| Field | Role |
|-------|------|
| `packageFor` | Matches ad type key (e.g. `listing-ticker-ad`) |
| `price` | Stored in cents; UI shows `$price/100` per duration label |
| `duration` | Day count used in package card (`X day(s)`) and `SelectDuration` limit |
| `behaviour` | National/state flags |

## UI display

`PackageCard`: `${pkg.price / 100}/${pkg.duration}day(s)`.

## Duration selection

`SelectDuration` modal opens after content step succeeds (`BuildAd` → `onSuccess`). Auctioneer picks start/end; `durationLimit` = `selectedPackage.duration` (max span for that package).

Checkout line uses `price: product?.price / 100` in `handleCheckout`.

## Billing

Product type label `Advertisement`; reference = created ad `_id`; includes `startDate`, `endDate`, `productId` (`AdvertisementID`).

No separate proration in wizard — price is the selected package flat fee for that duration tier.

User guide: [`user_side_doc/advertisement/pricing-and-duration.md`](../user_side_doc/advertisement/pricing-and-duration.md)
