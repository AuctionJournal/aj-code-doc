[Auction Journal](../index.md)

# Bidder Score

This module documents bidder scoring business logic in `AJ-Main-Backend`.

## Business Purpose

- Primary purpose: increase auctioneer confidence when allowing bidder participation.
- Provide a trust/reliability signal based on bidder behavior events.
- Keep a transparent score history so decisions can be audited.

## Current Backend-Aligned Baseline

- Bidder maintains aggregate score on bidder profile:
  - `scoreObtained` (current total)
  - `latestScoreObtained` (latest score change)
- Score timeline entries are stored in `BidderScore` model.
- Score is bounded to `-100..100`.
- Timeline supports cancellation of prior negative entries (`isCanceled`) when approval status changes.
- Score logs API returns paginated history + current total score.

## Event-Driven Scoring

Score changes are triggered by business events/reasons (examples from backend constants):
- Registration at Auction Journal
- Becoming Verified Bidder
- 15 Auctions registration
- Bid permission decline scenarios (non-payment and other reasons)
- Settlement and conduct-related negative events

## Scoring Values (Current Reference)

Current reason score values from backend constants:

- Registration at Auction Journal: `+10`
- Becoming Verified Bidder: `+25`
- Paying for won bids for more than 5 items: `+10`
- 15 Auctions registration: `+10`
- Winning & paying bidder at every auction from 1-5 items: `+1`
- No Payment at settlement: `-80`
- No show for lot pickup: `-5`
- Chargeback at settlement: `-80`
- Getting declined for all bids permanently (for no payment): `-80`
- Getting declined for all bids permanently (other than non-payment): `-75`
- Getting declined for one auction (for no payment): `-80`
- Getting declined for one auction (other than non-payment): `-20`

## Visibility Rules (Interview)

- Bidder-facing view: show full score details, including reasons/history.
- Auctioneer-facing view: show score value only (no full reason history).

## Policy Decisions (Interview)

- Keep raw score model as-is (no confidence band labels at this time).
- No bidder dispute/appeal flow for score events currently.
- Score is informational currently; no direct automatic enforcement actions are defined from score alone.

## Field Reference

- Detailed score model fields: [Fields](./fields.md)

## Known Boundary

- This doc focuses on bidder-score logic only.
- Bidder profile, verification, and permission workflows are documented in their own modules.

## Pending Interview Clarifications

- Whether any reasons should be hidden/redacted from bidder-facing logs in future.
