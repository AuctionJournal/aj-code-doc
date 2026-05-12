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

- Auction **status** moves to **closed**; **caches** for the auction and lots are cleared.
- **Default lot clerking** runs for lots that still have no clerk decision (sold vs pass / absentee high bid)—see **[After auction closes](./post-close.md)**.
- **Settlement** is generated in a separate auctioneer step when rules allow—see **[Settlement](./settlement.md)**.
