[Auction Journal](../README.md)

# Auction Features

1. Auctioneer - owner of auction

2. AuctionType - type of auction

   available types:
   "Online Timed Auction",
   "Online Absolute Auction",
   "Absentee Bidding",
   "Onsite With Live Webcast",

3. AuctionID - unique id of auction (nums and string)

4. Name - name of auction

5. Description - desc of auction

6. startDate - date auction will be visible to public and registration starts and only if published

7. endDate - everything of auction ends

8. lotType: { type: String, enum: ["Catalogued", "Non Catalogued"] },

9. isMultipleRingAuction: { type: Boolean },

10. TermsAndCondition - terms and condition of auction

11. ShortBPExplanation: { type: String, default: null },

12. Currency: { type: String, default: "USD", enum: ["USD"] },

13. collectTaxes: { type: Boolean, default: false },

14. Address1: { type: String, default: null },

15. Address2: { type: String, default: null },

16. City: { type: String, default: null },

17. State: { type: String, default: null },

18. Country: { type: String, default: null },

19. Zip: { type: Number, default: null },

20. AuctionImages: { type: Object, default: null },

21. Commission: { type: ObjectId, ref: "acMiscFormulas" },

22. BuyerPremium: { type: ObjectId, ref: "acMiscFormulas" },

23. lotReserveAmount: { type: Number, default: 0 },

24. BuyerTax: { type: ObjectId, ref: "acMiscFormulas" },

25. SellerTax: { type: ObjectId, ref: "acMiscFormulas" },

26. sellers: { type: [String] },

27. ShippingAvailability: {
    type: String,
    default: "no",
    enum: ["yes", "no", "specific"],
    },

28. ShippingAccount: { type: String, default: null },

29. ringOptionAssignBy: {
    type: String,
    enum: ["range by lot number", "range by sale order"],
    },
30. ringOptions: [
    {
    rangeBegin: { type: Number },
    rangeEnd: { type: Number },
    lotCount: { type: Number },
    lotsLeft: { type: Number },
    liveWebcast: { type: ObjectId, ref: "auctionLiveWebcast" },
    isRingLive: { type: Boolean },
    isRingPaused: { type: Boolean },
    isClosed: { type: Boolean },
    isCompletelyClosed: { type: Boolean },
    },
    ],
31. totalLots: { type: Number },

32. AccounType: { type: String, default: null },

33. previewDateTime: { type: String, default: null },

34. auctionDateTime: { type: String, default: null },

35. checkoutDateTime: { type: String, default: null },

36. biddingNotice: { type: Object, default: null },

37. auctionNotice: { type: Object, default: null },

38. paymentInformation: { type: String, default: null },

39. shippingPickUp: { type: String, default: null },

40. biddingType: {
    type: String,
    default: "Online Timed",
    enum: [
    "Online Timed",
    "Online Absolute",
    "Live Webcast",
    "Onsite",
    "Onsite With Live Webcast",
    "Absentee Bidding",
    ],
    },

41. reserveType: {
    type: String,
    default: "publicBidding",
    enum: ["publicBidding", "sealedBidding"],
    },

42. bidAmountType: {
    type: String,
    default: "maximumBidding",
    enum: ["maximumBidding", "flatBidding"],
    },

43. isPreBiddingAllowed: { type: Boolean },
44. isMultiDateBidding: { type: Boolean },
45. openPreBiddingAt: { type: Date },
46. biddingTimmings: { type: [biddingTimingSchema] },

47. TimeZone: {
    type: String,
    default: null,
    enum: [
    "Pacific/Honolulu",
    "America/Juneau",
    "America/Los_Angeles",
    "America/Boise",
    "America/Chicago",
    "America/Detroit",
    null,
    ],
    },

48. OpenBidding: { type: Date, default: null },

49. closebidding: { type: Date, default: null },

50. softCloseSeconds: {
    type: String,
    default: function () {
    return this.AuctionType !== "Onsite With Live Webcast"
    ? "00:00:00"
    : undefined;
    },
    },

51. bidSoftCloseSeconds: {
    type: String,
    default: function () {
    return this.AuctionType !== "Onsite With Live Webcast"
    ? "00:00:00"
    : undefined;
    },
    },

52. autoSyncGroupedLotSoftCloseTime: { type: Boolean, default: false },

53. showImmediateBidStatusOnClosedLots: { type: Boolean, default: false },

54. biddingIsTimeTheMoneyOnLotsWithAQuanityGreaterThanOne: {
    type: Boolean,
    default: false,
    },

55. bidderRegistrationType: {
    type: String,
    default: "verifiedBidder",
    enum: ["registeredBidder", "verifiedBidder"],
    },

56. startingBidcardNumber: { type: Number, default: 1 },

57. BidderPermission: { type: Number, default: null },

58. emailSubject: { type: String, default: null },

59. emailBody: { type: String, default: null },

60. bidIncrement: {
    type: [
    {
    _id: false,
    amount: {
    type: Number,
    required: true,
    },
    increment: {
    type: Number,
    required: true,
    },
    },
    ],
    default: [],
    },

61. requiredLastCreditCardAuthorizationInDays: { type: String, default: null },

62. bidIncrementType: {
    type: String,
    default: "Force Min Bid to Bid Increment Schedule",
    enum: [
    "Force Min Bid to Bid Increment Schedule",
    "Apply Bid Increments by Each",
    ],
    },

63. location: {
    type: {
    type: String, // Don't do `{ location: { type: String } }`
    enum: ["Point"], // 'location.type' must be 'Point'
    default: "Point",
    // required: true,
    },
    coordinates: {
    type: [Number],
    default: [0, 0],
    // required: true,
    },
    },

64. Status: {
    type: String,
    default: "Draft",
    enum: ["Draft", "Published", "Registration Open", "Live", "Closed"],
    },
65. isPublished: { type: Boolean, default: false },
66. isSettlementGenerated: { type: Boolean, default: false },
67. createdAt: false,
68. modiefiedAt: true,

#### biddingTimingSchema

startAt: {
type: Date,
required: true,
},
isClosed: {
type: Boolean,
default: false,
},
