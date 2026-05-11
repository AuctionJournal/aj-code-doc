[Auction Journal](../index.md)

# Newsletter

This module documents newsletter subscription business logic in `AJ-Main-Backend`.

## Business Purpose

- Capture email-based marketing consent for AJ communications.
- Classify subscribers by customer relationship:
  - `Auctioneer`
  - `Bidder`
  - `Non Customer`
- Support subscribe/unsubscribe lifecycle with persistent status tracking.

## Core Lifecycle

1. User submits email to subscribe.
2. System checks if email already exists in subscriber collection.
3. System identifies whether email belongs to a known bidder/auctioneer account.
4. Subscriber is stored/updated with:
   - `isSubscribed = true`
   - inferred `userType`
   - linked business id (`AuctioneerID` or `BidderID`) when available
5. Confirmation email is sent.
6. On unsubscribe request, system sets `isSubscribed = false`.

## Key Business Rules

- Email is required for subscription request.
- Duplicate active subscription attempts are blocked with user-facing message.
- Subscriber classification priority:
  - if email matches auctioneer -> `Auctioneer`
  - else if matches bidder -> `Bidder`
  - else -> `Non Customer`
- Unsubscribe is soft-state change (record retained, status flipped).

## Data Model (Business Meaning)

Primary model: `Newsletter` (`app/models/newsletter.js`)

- `email`: subscriber identity key (lowercase)
- `FirstName`: optional personalization field
- `isSubscribed`: active consent flag
- `userType`: relationship segment (`Auctioneer` / `Bidder` / `Non Customer`)
- `userId`: business identifier for known customers

## Communication Behavior

- Subscribe flow sends “subscribed successfully” email via notification mail pipeline.
- Unsubscribe flow currently updates DB status only (no explicit unsubscribe confirmation mail in this controller).

## Operational Notes

- Newsletter routes are public endpoints (no auth requirement).
- External Mailchimp package is configured in controller but primary persisted source of truth here is local `Newsletter` collection.
- Current implementation uses upsert-style persistence for subscribe, enabling idempotent create/update behavior.

## Minimal API Map (Reference Only)

- `POST /api/subscribeNewsletter`
- `POST /api/unsubscribeNewsletter`
