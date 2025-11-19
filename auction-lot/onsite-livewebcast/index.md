[Auction Journal](../../index.md)

# Fields

[click to view fields](./fields.md)

## Operations

- Open Lot
  - Suggested lot
  - Auto open next lot
- [Bidding](./bidding.md)
- Reset Bidding
- Clerk Lot

## Bidding

We support two types of bidders during an auction: **Internet Bidders** and **Floor Bidders**.

### 1. Internet Bidders

- Internet bidders can place bids directly through the **public website**.
- They participate in real time and submit bids using the **online bidding interface**.

Internet bidders have two ways to place bids during a live auction: **Flat Bid** and **Max Bid**.

### Flat Bid

- A **Flat Bid** is a standard bid placed at the _current bid amount_ or the _next available increment_.
- The bidder manually clicks to place each bid.
- Each click places **one increment** based on the auction’s bidding rules.

### Max Bid

- A **Max Bid** allows the bidder to set the **maximum amount** they are willing to pay for the lot.
- Once placed, the system will automatically bid on the bidder’s behalf:
  - It increases bids **incrementally**, only when needed.
  - It ensures the bidder remains the highest bidder until another bidder exceeds the max amount.
- Other bidders cannot see the max bid value—only that they have been outbid.

## Prebidding

Prebidding allows Internet Bidders to place bids **before the live auction starts**. This gives bidders the opportunity to participate even if they cannot join the live session.

### How Prebidding Works

- Internet bidders can place bids using the same **Flat Bid** or **Max Bid** options.
- All prebids are recorded and reflected in the lot’s **current highest bid** before the auction goes live.
- The highest prebid becomes the **starting bid** for the auctioneer during the live auction.

### Flat Bid in Prebidding

- Bidders can place a standard bid on the current prebid amount.
- Each Flat Bid follows the auction’s predefined **bid increments**.

### Max Bid in Prebidding

- Bidders may set a **Max Bid**, specifying the top amount they are willing to pay.
- The system auto-bids on their behalf against other prebidders, following normal increments.
- The Max Bid amount is **not visible** to other bidders.

### During Live Auction

- When the live session begins:
  - The highest prebid becomes the **opening bid**.
  - If a Max Bid was placed, the system will continue to auto-bid up to the bidder’s max amount.
  - The auctioneer must outbid any remaining max bid to take control of the bidding.

### 2. Floor Bidders

- Floor bidders are physically present at the auction venue.
- Their bids are **entered manually by the auctioneer** on their behalf.
- Floor bidders can bid using the **standard auction increments** or provide a **custom bid amount** to the auctioneer.

---

## Auctioneer Controls

### Bid Amount Options

- The auctioneer can ask for the next bid amount from the **Auction Controller**.
- The auctioneer can also enter a **custom bid amount** using the **Edit** option.

### Bid Increment Options

- The auctioneer can select the next bid increment from the **Auction Controller**.
- The auctioneer may also set a **custom bid increment** through the **Edit** option.

## Open Lot

- Auctioneer will get suggestion of next lot number to open
- Auctioneer can open suggested lot or different lot by lot number
- Next lot can be automatically opened after clerking if the feature is turned on
- Open lot validations
  - basic auctioneer, auction validation
  - lot validation and warning
    - is lot
    - is lot auction ready
    - is already opened
    - check is of different ring lot
    - check is already clerked lot

### Lot open diveations

- Opening Diff ring Lot
  - if opened then that lot become part of this ring
  - will be removed from actual ring
  - lot count of both ring will be updated
- Opening already clerked lot
  - If opened clerked status will be removed
  - lot will be opened again to bid and closebidding of lot updated to end of auction
  - lot count in ring will be updated
  - live biddings will be updated

## Reset Bidding

- live bidding in the lot will be reseted

## Clerk Lot

- before clerking lot fair warning will be given
- can be clerked to sold or pass
- after clerking close of lot will be updated to current time. thus lot will be shown as closed
- clerked lots and lots left count in ring will be updated

### Fair Warning

### Pass lot

- lot will be clerked as pass

### Sold lot

- lot will be clerked as sold
- can be sold to floor bidder or internet bidder
  - Internet bidder
  - Floor bidder
    - will ask for bid card no of registered floor bidder
    - then sold to that floor bidder
