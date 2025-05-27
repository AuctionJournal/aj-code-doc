[Auction Journal](../index.md)

# Auctioneer Fields

    CompanyName: { type: String },

    FirstName: { type: String },

    LastName: { type: String },

    StreetAddress: { type: String },

    City: { type: String },

    State: { type: String },

    Country: { type: String },

    ZipCode: { type: String },

    Email: { type: String, index: true, sparse: true },

    Email_otp: { type: String },

    Email_Expiry_time: { type: Date },

    is_EmailVerified: { type: Boolean, default: false },

    Phone: { type: String, required: true, unique: true },

    AlternateContact: { type: String },

    otp: { type: String, required: true },

    Expiry_time: { type: Date, required: true },

    is_PhoneVerified: { type: Boolean, default: false },

    Photo: { type: String, default: "" },

    AuctioneerID: { type: String, unique: true },

    AuctioneerLicensceNo: { type: String },

    AuctioneerLicenscePhoto: { type: Object },

    AuctioneerBio: { type: Object },

    Website: { type: String, default: null },

    facebook: { type: String, default: null },

    youtube: { type: String, default: null },

    instagram: { type: String, default: null },

    linkedin: { type: String, default: null },

    password: { type: String },

    tickerSymbol: { type: String },

    subscribedNewsletter: { type: Boolean, default: false },
    stripeCustomer: { type: String },
    stripeConnectedAccount: { type: String },
    isRegistered: { type: Boolean, default: false },
    isEligibleForFreeListing: { type: Boolean, default: false },
    websiteWithTag: { type: String, default: "" },
