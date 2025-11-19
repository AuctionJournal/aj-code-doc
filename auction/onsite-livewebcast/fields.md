[Auction Journal](../../index.md)

# Fields

## Livewebcast

- auctioneer - related auctioneer
- auction - realted auction
- ring - ring no
- isAuctioneerDisconnected - if disconnected and ring is live. ring will be auto closed in 1 min
- isLive - is ring live
- isInitialStarted - first start of ring
- isPaused - is ring paused by auctioneer
- isClosed - is ring closed for now by auctioneer
- isCompletelyClosed - if all lots in ring are clerked. ring will be completely closed
- isFairWarning - lot is about to clerk
- isShowReserveNotMetWarning - if true warning will be shown to user
- isAutoBidIncrement - if auto then it follows bidIncrementPattern else selected increment amount will be used
- isFloorBidderAvailable
- bidIncrementPattern - same as mentioned in related auction
- currentBidIncrementAmount - it is selected increment or increment from the pattern is isAutoBidIncrement is true
- lotsLeft - lots left to clerk in the ring
- clerkedLotsCount - no of lots clerked in the ring
- liveLot - currently opened lot live details
- currentLot - currently opened lot
- currentLotNumber: { type: Number },
- streamingDetails
  - isStreaming - is streaming by auctioneer
  - streamPlaybackUrl: { type: String, default: "" },
  - channelArn: { type: String, default: "" },
  - ingestEndpoint: { type: String, default: "" },
  - streamKey: { type: String, default: "" },
- viewersCount - no of users in room including auctioneer, presenter and public users
- notificationContent - notification which will come in public website in ring page
  - title
  - description
- isChatDisabledForBidder - true if disabled
- cannedMessages - array of canned messages
- chatMessages - array of all chat messages in that ring

versionKey: false,
timestamps: true,

## Chat

- message - message string
- type - type of message

  - possible types
    - "auctioneer" - message sent by auctioneer
    - "bidder" - message sent by bidder
    - "reply",
    - "normal",
    - "sold",
    - "reserve warning",
    - "fair warning",
    - "pause ring",

- bidder - bidder who sent this message, only if sent by bidder
- createdAt
