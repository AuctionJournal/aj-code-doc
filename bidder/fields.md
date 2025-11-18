const identityVerification = new mongoose.Schema(
{
identityType: {
type: String,
enum: ["Driving Licence", "State ID", "Passport"],
default: "Driving Licence",
},
imageFrontView: { type: String },
imageBackView: { type: String },
isVerified: { type: Boolean, default: false },
},
{ \_id: false }
);

const BidderSchema = new mongoose.Schema(
{
FirstName: { type: String },

    LastName: { type: String },

    UserName: { type: String },

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

    otp: { type: String, required: true },

    Expiry_time: { type: Date, required: true },

    is_PhoneVerified: { type: Boolean, default: false },

    DrivingLicenseNo: { type: String },

    DrivingLicensePhoto: {
      frontView: { type: String },
      backView: { type: String },
    },

    password: { type: String },

    BidderID: { type: String, unique: true },

    stripeCustomer: { type: String },
    stripeConnectedAccount: { type: String },

    Photo: { type: String, default: "" },

    identityVerification,
    scoreObtained: {
      type: Number,
      min: -100,
      max: 100,
      required: true,
      default: 0,
    },
    latestScoreObtained: {
      type: Number,
      min: -100,
      max: 100,
      required: true,
      default: 0,
    },

    subscribedNewsletter: { type: Boolean, default: false },
    isRegistered: { type: Boolean, default: false },
    isVerified: { type: Boolean, default: false },

},
{
versionKey: false,
timestamps: true,
}
);

BidderSchema.pre("save", function (next) {
if (this.scoreObtained < -100) {
this.scoreObtained = -100;
} else if (this.scoreObtained > 100) {
this.scoreObtained = 100;
}

next();
});
