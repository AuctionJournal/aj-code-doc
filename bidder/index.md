[Auction Journal](../index.md)

# Bidder

This module explains bidder business logic in `AJ-Main-Backend`, focused on trust, participation, and account lifecycle.

## Business Purpose

- Manage bidder identity and profile across the platform.
- Enforce trust checks before enabling full bidding behavior.
- Track bidder performance/reputation via score history.
- Power bidder dashboard insights (registrations, wins, spend, participation calendar).

## Core Lifecycle

1. Bidder starts via **public registration** (phone OTP, then `registerBidder`) — see [Registration](./registration.md).
2. Bidder account exists with profile + verification placeholders.
3. Bidder completes **verification** (Stripe card + ID upload, then platform verify) — see [Verification](./verification.md). Mandatory for many auctions: [Verification required](./verification-required.md). **No signup fee:** [Cost](./cost.md).
4. If both identity proof and card on file pass checks, bidder becomes platform-verified (`isVerified = true`) with score event and email.
5. Bidder continues participating in auctions/listings; dashboard and score logs reflect activity over time.

## Key Business Rules

- Verification is two-part: **identity verified + card on file**.
- Passport allows front image only; other identity types require both front and back.
- New bidders can exist but list selection APIs often require `isRegistered = true`.
- Public/internal bidder references are synchronized:
  - profile name/photo/identity changes are propagated to `ClientsOfAuctioneer` references.
- Bidder score is bounded in model constraints (`-100` to `100`).

## Identity and Trust Logic

- Identity update flow validates presence of required document images before accepting.
- On identity update, old stored blobs are cleaned up.
- Full bidder verification endpoint returns explicit failure state flags:
  - `isIdentityVerified`
  - `isCardVerified`
- Successful verification triggers:
  - `isVerified = true`
  - bidder score event creation (`verifyingAccount`)
  - verification email notification

## Participation and Dashboard Logic

- Dashboard counts include:
  - auctions registered
  - winning lots (open/active window check)
  - auctions watched (currently mirrors registrations)
  - total spent (settlement sum)
- Calendar marks daily participation when bidder has:
  - approved auction registrations, or
  - listing callback requests / listing bid pass records
- Auctioneer-facing bidder statics provide one-year and overall indicators:
  - registrations, approvals/declines, lots won, unique bids, recent registration reference

## Score Logic

- Bidder score logs exclude canceled records.
- Logs are paginated and enriched with related auctioneer and auction identity.
- Current total score is returned alongside timeline entries for UI consistency.

## Data Fields

- Detailed bidder schema fields and business meaning: [Fields](./fields.md)

## Minimal API Map (Reference Only)

- Registration (OTP + `registerBidder`): [Registration](./registration.md)
- Forgot password: [Authentication / Forgot password](../auth/forgot-password.md)
- Verification (Stripe card + ID + `verifyBidder`): [Verification](./verification.md)
- Profile: read/edit/photo update — [Profile](./profile.md)
- Dashboard: counts + calendar
- Reputation: score logs
- Auctioneer tools: bidder statics + bidder list
