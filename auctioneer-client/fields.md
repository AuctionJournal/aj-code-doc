[Auction Journal](../index.md)

# Auctioneer Client Fields

Source model: `AJ-Main-Backend/app/models/clientsOfAuctioneer.js`

## Identity and Ownership

- `clientCode` (required, unique)
- `auctioneer` (required, owner reference)
- `clientMail` (required, primary client identity key)
- `email` (optional alternate email field used in floor-bidder path)

Business meaning:
- Defines auctioneer-scoped client identity and uniqueness boundaries.

## Profile Core

- `companyName`
- `clientName`
  - `firstName`
  - `middleName`
  - `lastName`
- `phone1`, `phone2`
- `dlNumber`
- `dob`
- `notes`
- `auctionFoundFrom`

## Address Structure

- `clientAddress.billTo`
  - `address1`, `address2`, `city`, `state`, `zip`, `country`
- `clientAddress.shipTo`
  - `address1`, `address2`, `city`, `state`, `zip`, `country`

## Role and Linkage Flags

- `isFloorBidder`
- `isBuyer`
- `isConsigner`
- `isLinkedToBidder`
- `isBidderDataConnected`
- `buyerRef` (linked `Bidder` reference)

Business meaning:
- A single client may hold multiple roles simultaneously.
- Bidder linkage enables cross-model sync and permission/scoring workflows.

## Identity Proof and Media

- `imageProvided`
- `idCardImageFrontView`
- `idCardImageBackView`
- `identityType` (`Driving Licence` / `State ID` / `Passport`)
- `buyerIdentification`
  - `identityType`
  - `imageFrontView`
  - `imageBackView`

## Accounting Block

### Buyer Accounting

- `reservedBidCard`
- `isTaxable`
- `taxExempt`
- `taxExpires`
- `isDefaultBuyerPremium`
- `buyerPremium` (formula reference)
- `buyerPermission`
- `buyerPermissionStatus` (`Default` / `Approved` / `Declined`)
- `isShareContact`
- `buyerPermissionPublicNotes`
- `buyerPermissionPrivateNotes`
- `buyerPermissionDeclinedReason`
- `buyerPermissionModifiedAt`

### Consigner Accounting

- `isSpecificBuybackSetting`
- `comission` (formula reference)
- `isTaxExempt`
- `sellerTax` (formula reference)
- `taxIDExpireDate`

## Index and Uniqueness Notes

- Indexed by `auctioneer`.
- Composite uniqueness includes auctioneer-scoped client identity/accounting dimensions:
  - `auctioneer`
  - `accounting.buyer.reservedBidCard`
  - `clientMail`
  - `clientCode`
  - `buyerRef`

## System Metadata

- `createdAt`, `updatedAt` (timestamps enabled)
- `versionKey` disabled
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
