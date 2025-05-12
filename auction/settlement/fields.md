[Auction Journal](../../index.md)

# Fields

const auctionCharges =
{
account: { type: ObjectId, ref: "AuctioneerMiscAccount" },
description: { type: String },

    chargeType: {
      type: String,
      enum: ["Fixed", "Percentage"],
      default: "Fixed",
    },
    fixedAmount: { type: Number },

    percentage: { type: Number },
    amount: { type: Number },

}

const extraLotCharges =
{
account: { type: ObjectId, ref: "AuctioneerMiscAccount" },
description: { type: String },

    chargeType: {
      type: String,
      enum: ["Fixed", "Percentage"],
      default: "Fixed",
    },
    fixedAmount: { type: Number },

    percentage: { type: Number },
    amount: { type: Number },

}

const lotCharges =
{
lot: { type: ObjectId, ref: "lotdetails", required: true },
hammerPrice: { type: Number },

    //buyerPremium - for buyer only
    buyerPremium: { type: ObjectId, ref: "acMiscFormulas" },
    buyerPremiumPercentage: { type: Number },
    buyerPremiumAmount: { type: Number },

    //buyerTax - for buyer only
    buyerTax: { type: ObjectId, ref: "acMiscFormulas" },
    buyerTaxPercentage: { type: Number },
    buyerTaxAmount: { type: Number },

    //commission - for seller only
    commission: { type: ObjectId, ref: "acMiscFormulas" },
    commissionPercentage: { type: Number },
    commissionAmount: { type: Number },

    //sellerTax - for seller only
    sellerTax: { type: ObjectId, ref: "acMiscFormulas" },
    sellerTaxPercentage: { type: Number },
    sellerTaxAmount: { type: Number },

    extraLotCharges: [extraLotCharges],
    totalAmount: { type: Number },

}

const adjustment =
{
account: { type: ObjectId, ref: "AuctioneerMiscAccount" },
description: { type: String },
chargeType: {
type: String,
enum: ["Fixed", "Percentage"],
default: "Fixed",
},
fixedAmount: { type: Number },
percentage: { type: Number },
usedFor: {
type: String,
enum: ["Discounts", "Surcharges"],
},
amount: { type: Number },
}

const paymentReceipts =
{
paymentMethod: { type: String, required: true },
amountPaid: { type: Number, required: true },
notes: { type: String },
}

const auctionSettlementSchema =
{
auctioneer: { type: ObjectId, ref: "Auctioneer" },
auction: { type: ObjectId, ref: "auctionDetail", required: true },
settlementFor: { type: String, enum: ["Buyer", "Seller"], required: true },
bidder: { type: ObjectId, ref: "Bidder" },
seller: { type: ObjectId, ref: "clientsOfAuctioneer" },
auctionRegistration: { type: ObjectId, ref: "auctionsOfBidder" },
auctioneerClient: { type: ObjectId, ref: "clientsOfAuctioneer" },
lotCount: { type: Number },
auctionCharges: [auctionCharges],
lotCharges: [lotCharges],
adjustment,
invoiceNumber: { type: String },
totalAuctionChargesAmount: { type: Number, default: 0 },
totalLotChargesAmount: { type: Number, default: 0 },
totalAmount: { type: Number },
grandTotalAmount: { type: Number },
totalPaidAmount: { type: Number, default: 0 },
balanceDue: { type: Number },
reciptStatus: { type: String, enum: ["Paid", "Unpaid"], default: "Unpaid" },
paymentReceipts: [paymentReceipts],
}
