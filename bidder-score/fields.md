[Auction Journal](../index.md)

# Bidder Score Fields

Source references:
- `AJ-Main-Backend/app/models/bidderScore.js`
- `AJ-Main-Backend/app/models/Bidder.js` (aggregate score fields)

## Bidder Aggregate Score Fields (`Bidder` model)

- `scoreObtained`
  - current aggregate bidder score
  - bounded between `-100` and `100`
- `latestScoreObtained`
  - latest score delta applied

Business meaning:
- These fields represent the current confidence signal consumed by product views and decisions.

## Bidder Score Timeline Fields (`BidderScore` model)

- `bidder`: bidder reference
- `scoreObtained`: score delta for the event entry
- `previousScore`: bidder score before event application
- `isIncreament`: direction marker for increase/decrease event
- `reason`: enum reason for why score changed
- `furtherInfo`: optional contextual metadata
- `availableReference`: linked source record id for contextual events
- `referenceRelation`: linked source type (`auctionsOfBidder` or `clientsOfAuctioneer`)
- `isCanceled`: logical cancellation marker for reversible score entries
- `createdAt`, `updatedAt`: timeline metadata

## Score Bounds

- Entry-level and aggregate score behavior is constrained to `-100..100`.
- Canceled entries remain in timeline for auditability but are excluded in active score-log views.
