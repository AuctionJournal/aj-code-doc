## Auction Lifecycle

- Registration Start
- Open Bidding
- Live Bidding (Live Webcast Auction)
- Soft Starts
- Auction Close

## Registration Start

- Auction Status will be updated to registration open
- In redis lots count will be initialised
- In Cache upcoming auction will be invalidated

## Open Bidding

- All auction except onsite with live webcast, and onsite with live webcast auction with prebidding will have open bidding.
- Auction status will be updated to live.
- In cache feature auction will be invalidated.

## Live Bidding

- Auctioneer can start live bidding.
- At midnight of live bidding according to timezone live bidding will be automatically closed.

## Soft Close Starts

- Auct

## Auction Close

- Auction Status will updated to closed
- All caches related to auction and lots will be deleted.
- Lots clerking will be done.
-
