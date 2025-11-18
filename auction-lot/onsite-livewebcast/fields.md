const mongoose = require("mongoose");
const { ObjectId } = mongoose.Schema.Types;
const mongoosePaginate = require("mongoose-paginate-v2");

const biddings = new mongoose.Schema(
{
bidAmount: { type: Number },
bidderType: {
type: String,
enum: ["Pre Bidding", "Internet Bidder", "Floor Bidder"],
},
bidder: { type: mongoose.Schema.Types.ObjectId, ref: "Bidder" },
},
{ \_id: false, timestamps: true }
);

const auctionLotLiveWebcastSchema = new mongoose.Schema(
{
auctioneer: { type: ObjectId, ref: "Auctioneer" },
auction: { type: ObjectId, ref: "auctionDetail", required: true },
lot: { type: ObjectId, ref: "lotdetails", required: true },
lotNumber: { type: Number },
liveWebcast: { type: ObjectId, ref: "auctionLiveWebcast" },
ring: { type: Number, required: true },
isItOfDiffRing: { type: Boolean },
winingPreBidAmount: { type: Number },
winningPreBidder: { type: ObjectId, ref: "Bidder" },
winnerPreBidCardNo: { type: Number },
preBidcount: { type: Number, default: 0 },
reserveAmount: { type: Number },
highestMaximumBidAmount: { type: Number, default: 0 },
currentBidAmount: { type: Number, required: true },
nextBidAmount: { type: Number },
winnerType: {
type: String,
enum: ["Pre Bidding", "Internet Bidder", "Floor Bidder"],
},
winningBidder: { type: ObjectId, ref: "Bidder" },
winningFloorBidder: { type: ObjectId, ref: "clientsOfAuctioneer" },
winnerBidCardNo: { type: Number },
isBiddingLocked: { type: Boolean },
bidCount: { type: Number, default: 0 },
biddings: { type: [biddings], default: [] },
clerkStatus: {
type: String,
default: null,
enum: [null, "Sold", "Pass"],
},
},
{
versionKey: false,
timestamps: true,
}
);

auctionLotLiveWebcastSchema.plugin(mongoosePaginate);
module.exports = mongoose.model(
"auctionLotLiveWebcast",
auctionLotLiveWebcastSchema
);
