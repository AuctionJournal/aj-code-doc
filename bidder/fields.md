# Bidder Fields

Source model: `AJ-Main-Backend/app/models/Bidder.js`

## Identity Verification (nested object)

- `identityType`: one of `Driving Licence`, `State ID`, `Passport`
- `imageFrontView`: front document image blob/link
- `imageBackView`: back document image blob/link
- `isVerified`: identity proof validity flag

Business rule:
- Passport can work with front image only; other types need front + back image.

## Profile and Contact

- `FirstName`, `LastName`, `UserName`
- `StreetAddress`, `City`, `State`, `Country`, `ZipCode`
- `Email` (indexed), `Phone` (required, unique)
- `Photo`

Business meaning:
- Core bidder profile used for account display and downstream buyer references.
- Profile photo/name changes are propagated to auctioneer-side client references.

## Auth and OTP State

- `password`
- `otp`, `Expiry_time`, `is_PhoneVerified`
- `Email_otp`, `Email_Expiry_time`, `is_EmailVerified`

Business meaning:
- Tracks phone/email verification lifecycle and temporary OTP challenge state.

## Business IDs and Payments

- `BidderID` (unique business identifier)
- `stripeCustomer`
- `stripeConnectedAccount`

Business meaning:
- Stripe linkage is required for card-based verification checks.

## Trust and Reputation

- `isRegistered`: bidder completed registration status
- `isVerified`: bidder passed platform trust checks
- `scoreObtained`: aggregate bidder score (bounded `-100` to `100`)
- `latestScoreObtained`: most recent score delta/state

Business rule:
- `scoreObtained` is clamped in pre-save hook to remain within `-100..100`.

## Preferences

- `subscribedNewsletter`

## System Metadata

- `createdAt`, `updatedAt` (timestamps enabled)
- `versionKey` disabled
