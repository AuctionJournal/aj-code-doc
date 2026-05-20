[Payment](./index.md) · [Auction Journal](../index.md)

# Why should a bidder add card details? How do they add them? Is it safe?

This page explains bidder card-on-file behavior used in verification and auction participation.

## Why bidders should add card details

Card details are required because they are part of bidder verification and auction operations:

1. Verification requirement:
   - Bidder verification requires both:
     - identity verification (`isIdentityVerified`)
     - card on file (`isCardVerified`)
   - Platform marks bidder as verified only when both checks pass.
2. Auction participation:
   - Many auction registration flows are configured for verified bidders, so card setup helps bidder participate in more auctions.
3. Payment collection:
   - Stored card/payment rails are used where auctioneer or platform needs to collect eligible payments in transaction flows.

## Where this happens in frontend

Codebase: `auctionjournal-public`

- Route: `/bidder/verification`
- Main components:
  - `src/app/Components/Bidder/Bidder_Verification/Unverified_Bidder`
  - `src/app/Components/Payments/Stripe/CreatePaymentMethod`
  - `src/app/Components/Payments/PaymentCard`

Bidder flow:

- Open verification page.
- Add billing details and card in Step 1.
- Continue with identity upload in Step 2.
- Submit verification.

## Backend APIs involved (`AJ-Main-Backend`)

- `POST /api/payment/method/create`
  - Creates Stripe setup intent for card setup.
- `POST /api/payment/getCardDetails`
  - Fetches saved bidder cards for verification/manage UI.
- `POST /api/bidder/verify`
  - Final verification check; requires ID verified + card present.
- `POST /api/bidder/profile/identity/update`
  - Saves ID images used in verification.

## Is it safe?

Business and technical safety notes:

- Card entry is handled with Stripe elements flow; raw card number is not stored directly in Auction Journal application data.
- Auction Journal stores Stripe-linked payment references and billing metadata needed for operations.
- Verification endpoint checks only whether card exists and identity is verified before enabling `isVerified`.

## Related behavior

- If card is missing, verify response can fail with `isCardVerified: false`.
- After verification, bidder can manage saved cards (add/update/delete) from verification UI.
