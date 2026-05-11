[Auction Journal](../index.md)

# Auctioneer

This module explains auctioneer business logic in `AJ-Main-Backend`, focused on onboarding, operational readiness, and auction creation eligibility.

## Business Purpose

- Represent seller-side business accounts that run auctions/listings.
- Capture company identity, licensing, contact, branding, and payout setup.
- Enforce readiness checks before an auctioneer can create auctions.
- Provide searchable public auctioneer presence and profile metadata.

## Core Lifecycle

1. Auctioneer starts account via phone-based pre-registration flow.
2. Full registration completes business profile, license details, credentials, and newsletter option.
3. System marks account as registered (`isRegistered = true`) and sends email verification flow.
4. Default miscellaneous accounts are provisioned for operational setup.
5. Auctioneer configures required business prerequisites (invoice details, seller/client setup, formulas, Stripe Connect).
6. Eligibility check gates auction creation until all mandatory prerequisites are present.

## Key Business Rules

- Registration cannot be replayed once account is already registered (anti-malicious guard).
- Phone verification is mandatory before full registration completion.
- Email must be unique and enters verification lifecycle after signup.
- `tickerSymbol` is generated from company/city during registration.
- Free-listing and auction creation readiness are controlled by backend flags and setup checks (not only UI).

## Auction Creation Eligibility Logic

Eligibility check (`auctioneerEligiblityToCreateAuction`) evaluates:

- Stripe Connect account is fully usable:
  - account exists
  - `details_submitted`
  - `charges_enabled`
  - `payouts_enabled`
- At least one seller/client exists.
- Invoice details exist.
- Required formula families exist:
  - Commission
  - Buyer Premium
  - Buyer Tax
  - Seller Tax

Special case:
- For `Absentee Bidding`, eligibility is relaxed to seller existence only.

## Profile and Public Presence

- Auctioneer can maintain profile metadata (company/person name, bio, website, socials, photo).
- Public bio fetch endpoint exposes curated profile fields for listing/detail pages.
- Search and grouping flows return auctioneer cards with computed activity counts (auctions/listings).

## Operational Configuration Domains

- **Invoice setup**: receipt title/message, billing/shipping requirements, contact/address metadata.
- **Locations**: multi-location management for auction operations.
- **Misc accounts/formulas/clients**: foundational setup used by auction and settlement workflows.

## Data Fields

- Detailed schema fields and business meaning: [Fields](./fields.md)
- Free listing publish gate and eligibility flow: [Free Listing Eligibility](./free-listing-eligibility.md)

## Minimal API Map (Reference Only)

- Registration and verification (auth module)
- Profile read/edit/photo/public bio
- Invoice + location configuration
- Auctioneer search/discovery
- Eligibility gate for auction creation
