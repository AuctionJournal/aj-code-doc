[Auction Journal](../../index.md)

# Fields

## Lot Livewebcast

- auctioneer - related auctioneer
- auction - related auction
- lot - related lot
- lotNumber
- liveWebcast - related ring details
- ring - ring no
- isItOfDiffRing - is this lot actually belong to diff ring
- winingPreBidAmount
- winningPreBidder
- winnerPreBidCardNo
- preBidcount
- reserveAmount - reserve amount of lot
- highestMaximumBidAmount - in pre bid
- currentBidAmount
- nextBidAmount
- winnerType
  - enum: ["Pre Bidding", "Internet Bidder", "Floor Bidder"],
- winningBidder - if pre bidding or internet bidder, mention the current winning bidder
- winningFloorBidder - if floor bidder, mention the current winning floor bidder
- winnerBidCardNo
- isBiddingLocked - locked during
- bidCount
- biddings - array of live bids
- clerkStatus
  - default: null,
  - enum: [null, "Sold", "Pass"]
- createdAt: true,
- modiefiedAt

## Bidding

- bidAmount
- bidderType - type of bidder
  - possible types: ["Pre Bidding", "Internet Bidder", "Floor Bidder"],
- bidder - if pre bidding or internet bidder, mention that bidder who bidded this amount
- createdAt - time of bidding
