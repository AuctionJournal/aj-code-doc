const mongoose = require("mongoose");
const { ObjectId } = mongoose.Schema.Types;
const mongoosePaginate = require("mongoose-paginate-v2");

const chatMessages = new mongoose.Schema(
{
message: { type: String },
type: {
type: String,
enum: [
"auctioneer",
"bidder",
"reply",
"normal",
"sold",
"reserve warning",
"fair warning",
"pause ring",
],
},
bidder: { type: ObjectId, ref: "Bidder" },
createdAt: { type: Date, default: Date.now },
},
{ \_id: false, timestamps: false }
);

auctioneer - related auctioneer
auction - realted auction
ring - ring no
isAuctioneerDisconnected: { type: Boolean, default: false },
isLive: { type: Boolean, default: true },
isInitialStarted: { type: Boolean, default: false },
isPaused: { type: Boolean, default: true },
isClosed: { type: Boolean, default: false },
isCompletelyClosed: { type: Boolean, default: false },
isFairWarning: { type: Boolean, default: false },
isShowReserveNotMetWarning: { type: Boolean },
isAutoBidIncrement: { type: Boolean, default: true },
isFloorBidderAvailable: { type: Boolean, default: false },
bidIncrementPattern: { type: [Object] },
currentBidIncrementAmount: { type: Number },
lotsLeft: { type: Number },
clerkedLotsCount: { type: Number, default: 0 },
liveLot: { type: ObjectId, ref: "auctionLotLiveWebcast" },
currentLot: { type: ObjectId, ref: "lotdetails" },
currentLotNumber: { type: Number },
streamingDetails: {
isStreaming: { type: Boolean },
streamPlaybackUrl: { type: String, default: "" },
channelArn: { type: String, default: "" },
ingestEndpoint: { type: String, default: "" },
streamKey: { type: String, default: "" },
},
viewersCount: { type: Number, default: 0 },
notificationContent: {
title: { type: String },
description: { type: String },
},
isChatDisabledForBidder: { type: Boolean },
cannedMessages: { type: [String] },
chatMessages: { type: [chatMessages], default: [] },
},
{
versionKey: false,
timestamps: true,
}
);

auctionLiveWebcastSchema.plugin(mongoosePaginate);
module.exports = mongoose.model("auctionLiveWebcast", auctionLiveWebcastSchema);
