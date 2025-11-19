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
