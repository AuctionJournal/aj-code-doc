const mongoose = require("mongoose");
const { ObjectId } = mongoose.Schema.Types;
const mongoosePaginate = require("mongoose-paginate-v2");

const clientName =
{
firstName: { type: String },
middleName: { type: String },
lastName: { type: String },
}

const billTo =
{
address1: { type: String },
address2: { type: String },
city: { type: String },
state: { type: String },
zip: { type: Number },
country: { type: String },
}

const shipTo =
{
address1: { type: String },
address2: { type: String },
city: { type: String },
state: { type: String },
zip: { type: Number },
country: { type: String },
}

const clientAddress =
{
billTo,
shipTo,
}

const buyer =
{
reservedBidCard: { type: Number, default: null },
isTaxable: { type: Boolean, default: true },
taxExempt: { type: String, default: null },
taxExpires: { type: String, default: null },
isDefaultBuyerPremium: { type: Boolean, default: true },
buyerPremium: { type: ObjectId, ref: "acMiscFormulas" },
buyerPermission: { type: Number, default: null },
buyerPermissionStatus: {
type: String,
enum: ["Default", "Approved", "Declined"],
default: "Default",
},
isShareContact: { type: Boolean, default: false },
buyerPermissionPublicNotes: { type: String },
buyerPermissionPrivateNotes: { type: String },
buyerPermissionDeclinedReason: { type: String },
buyerPermissionModifiedAt: { type: Date, default: Date.now },
}

const consigner =
{
isSpecificBuybackSetting: { type: Boolean, default: false },
comission: { type: ObjectId, ref: "acMiscFormulas" },
isTaxExempt: { type: Boolean, default: false },
sellerTax: { type: ObjectId, ref: "acMiscFormulas" },
taxIDExpireDate: { type: String },
}

const accounting =
{
buyer,
consigner,
}

const buyerIdentification =
{
identityType: {
type: String,
enum: ["Driving Licence", "State ID", "Passport"],
default: "Driving Licence",
},
imageFrontView: { type: String },
imageBackView: { type: String },
}

const clientsOfAuctioneer =
clientCode: { type: String, required: true, unique: true },
auctioneer: { type: ObjectId, ref: "Auctioneer", required: true },
clientMail: { type: String, required: true },
companyName: { type: String },
clientName,
clientAddress,
accounting,
buyerIdentification,
phone1: { type: String },
phone2: { type: String },
dlNumber: { type: String },
dob: { type: String },
notes: { type: String },
auctionFoundFrom: { type: String },
isBuyer: { type: Boolean, default: false },
isConsigner: { type: Boolean, default: false },
isBidderDataConnected: { type: Boolean, default: false }, // is data taken from bidder collection
buyerRef: { type: ObjectId, ref: "Bidder", default: null },
isLinkedToBidder: { type: Boolean, default: false }, // is linked to bidder
imageProvided: { type: String },
idCardImageFrontView: { type: String },
idCardImageBackView: { type: String },
identityType: {
type: String,
enum: ["Driving Licence", "State ID", "Passport"],
default: "Driving Licence",
},
