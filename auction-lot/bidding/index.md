[Auction Journal](../../index.md)

# Auction lot bidding

## Business purpose

**Bidding** is how price discovery happens on a **lot**: registered bidders place offers within **time**, **increment**, and **permission** rules; the **lot** and **Bidding** records store state and history. Different **auction types** use different surfaces (online timed vs onsite live webcast).

## Scope of these docs

| Topic | Doc |
|--------|-----|
| Online **flat** and **max** bidding (timed / pre-bid API) | [Online bidding on a lot](./create.md) |
| **Auctioneer** — accept / pending / decline bid, edit max | [Edit bidding](./edit-bidding.md) |
| `Bidding` model fields | [Bidding fields](./fields.md) |
| **Live webcast** ring / floor bidding | [Onsite live webcast bidding](../onsite-livewebcast/bidding.md) |

## Prerequisites (online)

- Bidder **[registration](../../auction/registration.md)** for the auction, with **approved** permission when bidding is allowed.
- Auction **open bidding** and lot **close** window; lot **visible**; increment and **flat vs max** mode set on the auction.

## Lot model (runtime fields)

High bid, next increment, winner, counts: see [Auction lot fields](../fields.md) (bidding state section).
