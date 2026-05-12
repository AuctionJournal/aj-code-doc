[Auction Journal](../index.md)

# Auction registration — stored fields

Source model: `AJ-Main-Backend/app/models/auctionsOfBidder.js` (Mongoose model `auctionsOfBidder`). This is the **auction registration** record linking a bidder to an auction under an auctioneer client.

| Field | Type | Notes |
|--------|------|--------|
| `auctioneer` | ObjectId → Auctioneer | |
| `auction` | ObjectId → auctionDetail | Required |
| `bidder` | ObjectId → Bidder | |
| `client` | ObjectId → clientsOfAuctioneer | Required |
| `noteToAuctioneer` | String | Bidder message when online; floor onboard may use a fixed label (e.g. “Floor Bidder”) |
| `paymentMethod` | String | Stored id / reference for the card used at registration |
| `bidPermissionStatus` | String (enum) | `Pending`, `Approved`, `Declined`, `Permanently Approved`, `Permanently Declined` |
| `isShareContact` | Boolean | Default `false` |
| `declineReason` | String | |
| `permissionPrivateNote` | String | |
| `permissionPublicNote` | String | |
| `bidderScoreDuringRegistration` | Number | Required |
| `bidCardNumber` | Number | Required; unique per auction with other registrations |
| `bidderPermission` | Number | Spending cap; may be unset when declined |
| `bidderPermissionFrom` | String (enum or null) | `invitationEmail`, `auctioneerClient`, `auction`, `auctionRegistration` |
| `bidPermissionModifiedAt` | Date | Default now |
| `bidderIPAddress` | String | Captured at registration |
| `isFloorBidder` | Boolean | True when created via auctioneer **floor bidder** onboard for **onsite live webcast**; otherwise typical online registration |

Unique indexes: `(auction, bidCardNumber)`, `(bidder, auction)`.
