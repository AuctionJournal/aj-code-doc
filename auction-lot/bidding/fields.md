[Auction Journal](../../index.md)

# Auction Lot Flat/Max Bidding Fields

bidder: { type: ObjectId, ref: "Bidder", required: true },
lot: { type: ObjectId, ref: "lotdetails", required: true },
auction: { type: ObjectId, ref: "auctionDetail" },
auctioneer: { type: ObjectId, ref: "Auctioneer" },
auctioneerClient: { type: ObjectId, ref: "clientsOfAuctioneer" },
bidding: [bidding],
maxBidAmount: { type: Number, min: 0 },
acceptanceStatus: {
type: String,
enum: ["Accepted", "Pending", "Declined"],
default: "Accepted",
},
isClerkUpdated: { type: Boolean },

bidding =
{
maxBidAmount: { type: Number },
bidAmount: { type: Number, required: true },
bidAmountType: {
type: String,
enum: ["maximumBidding", "flatBidding", "autoBidding", "clerkingEdit"],
},
}

# Auction Lot Live Bidding Fields
