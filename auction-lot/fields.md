[Auction Journal](../index.md)

# Auction Lot Fields

- seller

  - Info - auctioneer client who is selling this lot

- commission

  - Info - formula applicable for commission
  - logic - if seller have specific buy back setting and seller have specific commission then that will be used else auction's default will be used

- buyerTax

  - Info - formula applicable for buyer tax
  - logic - auction's default will be used

- buyersPermium

  - Info - formula applicable for buyers premium
  - logic - auction's default will be used

- isSellerTaxable

  - Info - check if seller can be taxed
  - logic - taxable if seller have specific buy back setting and no tax exempt. Else if seller have no specific buy back setting and auction is not collecting taxes

- SellerTax

  - Info - formula applicable for seller tax
  - logic - if seller have specific buy back setting and seller have specific seller tax then that will be used else auction's default will be used
  - changable - if auction is collecting taxes and lot is taxable and before auction close bidding

        Auctioneer: { type: ObjectId, ref: "Auctioneer" },

        auctionDetailId: { type: ObjectId, ref: "auctionDetail" },

        lotNumber: { type: Number, default: null },

        saleOrder: { type: Number, default: null },

        quantity: { type: Number, default: 1 },

        seller: { type: Object, default: null },

        title: { type: String, default: null },

        category: { type: ObjectId, default: null, ref: "lotcategory" },

        categoryDetail: { type: String, default: null },

        description: { type: String, default: null },

        closeBidding: { type: Date, default: null },

        softCloseMilliSecs: { type: Number, default: 0 },
        bidSoftCloseMilliSecs: { type: Number, default: 0 },
        softCloseSeconds: { type: String, default: null },
        bidSoftCloseSeconds: { type: String, default: null },

        lotMedia: { type: Object, default: null },

        suggestedValueMinRange: { type: Number, default: null },

        suggestedValueMaxRange: { type: Number, default: null },

        reserve: { type: Number, default: null },

        startBid: { type: Number, default: null },

        currentBidAmount: { type: Number, required: true, default: 0 },
        nextBidAmount: { type: Number, required: true, default: 0 },
        highestMaximumBidAmount: { type: Number, default: 0 },
        lotWinner: { type: ObjectId, ref: "Bidder" },
        bidCount: { type: Number, default: 0 },

        clerkStatus: {
          type: String,
          default: null,
          enum: [null, "Sold", "Pass", "Hold", "High Bid"],
        },

        featured: { type: Boolean, default: false },

        shipping: { type: Boolean, default: false },

        seller: { type: ObjectId, default: null, ref: "clientsOfAuctioneer" },

        commission: { type: ObjectId, ref: "acMiscFormulas" },

        SellerTax: { type: ObjectId, ref: "acMiscFormulas" },

        isSellerTaxable: { type: Boolean, default: true },

        sellerLotExpenses: { type: [sellerLotExpensesSchema], default: [] },

        salesPerson: { type: String, default: null },

        salesPersoncommission: { type: String },

        buyersPermium: { type: ObjectId, ref: "acMiscFormulas" },

        buyerTax: { type: ObjectId, ref: "acMiscFormulas" },

        buyerLotCharge1: { type: String, ref: "acMiscFormulas" },

        buyerLotCharge2: { type: String, ref: "acMiscFormulas" },
        isHidden: { type: Boolean },
        isClerkUpdated: { type: Boolean, default: false },
        isLotClosed: { type: Boolean },
        isAuctionClosed: { type: Boolean, default: false },
        isForQROnly: { type: Boolean },
        isAuctionReady: { type: Boolean, default: false },
        clerkUpdatedAt: { type: Date, default: null },
        viewCount: { type: Number, required: true, default: 0 },
        watchlistCount: { type: Number, required: true, default: 0 },
