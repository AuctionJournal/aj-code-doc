-- Auctioneer: { type: ObjectId, ref: "Auctioneer" },

-- ListingID: { type: String, unique: true },

-- AuctionType: {
type: String,
enum: [
"OnSite Auction",
"Live Webcast Auction",
"Online Timed Auction",
"Online Absolute Auction",
"Live Webcast with OnSite Auction",
],
},

-- AuctionTitle: { type: String },

-- AuctionMonthYear: { type: String },

-- AuctionDate: { type: Date },

-- AuctionTime: { type: Date },

-- Address1: { type: String, default: null },

-- Address2: { type: String, default: null },

-- City: { type: String },

-- State: { type: String },

-- Country: { type: String },

-- Zip: { type: String },

-- location: {
type: {
type: String, // Don't do `{ location: { type: String } }`
enum: ["Point"], // 'location.type' must be 'Point'
required: true,
},
coordinates: {
type: [Number],
required: true,
},
},

-- AuctionCategory: { type: String },

-- CategoryDetails: { type: String },

-- imagePathId: { type: String },

-- NameOfProduct: { type: String },

-- uploadPhoto: { type: [Object], default: [] },

-- ProductDescription: { type: Object },

-- BiddingNotice: { type: Object },

-- AuctionNotice: { type: Object },

-- TermsAndCondition: { type: Object },

-- PaymentInfo: { type: ObjectId, ref: "Purchase_order" },

-- isPublished: { type: Boolean, default: false },

-- price: { type: Number },
