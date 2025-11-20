[Auction Journal](../../index.md)

# Fields

[click to view fields](./fields.md)

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
