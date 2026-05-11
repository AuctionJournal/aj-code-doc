[Auction Journal](../index.md)

# Auctioneer Fields

Source model: `AJ-Main-Backend/app/models/Auctioneer.js`

## Business Identity

- `CompanyName`
- `FirstName`, `LastName`
- `AuctioneerID` (unique business identifier)
- `tickerSymbol`

Business meaning:
- Represents both legal/business brand and contact person identity used in public and internal views.

## Contact and Address

- `StreetAddress`, `City`, `State`, `Country`, `ZipCode`
- `Email` (indexed, lowercase), `Phone` (required, unique), `AlternateContact`

Business meaning:
- Primary communication and geo identity for discovery/search and document generation.

## Verification and Auth State

- `otp`, `Expiry_time`, `is_PhoneVerified`
- `Email_otp`, `Email_Expiry_time`, `is_EmailVerified`
- `password`
- `isRegistered`

Business meaning:
- Tracks onboarding and verification lifecycle gates before full operational access.

## License and Profile Content

- `AuctioneerLicensceNo`
- `AuctioneerLicenscePhoto`
- `AuctioneerBio`
- `Photo`

Business meaning:
- Establishes trust credentials and public-facing profile for auctioneer pages.

## Web and Social Presence

- `Website`, `websiteWithTag`
- `facebook`, `youtube`, `instagram`, `linkedin`

Business meaning:
- Used for public branding, credibility, and external navigation.

## Payments and Commercial Eligibility

- `stripeCustomer`
- `stripeConnectedAccount`
- `isEligibleForFreeListing`

Business meaning:
- Stripe linkage supports payout/commercial workflows and auction-creation prerequisites.
- Free listing eligibility is a business entitlement flag.

## Preferences and Notifications

- `subscribedNewsletter`
- `notification.isNotify`
- `notification.auctionID`

Business meaning:
- Stores communication preference and lightweight auction notification state.

## System Metadata

- `createdAt`, `updatedAt` (timestamps enabled)
- `versionKey` disabled
