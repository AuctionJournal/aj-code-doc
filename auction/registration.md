[Auction Journal](../index.md)

# Auction

## Fields

    auctioneer - related auctioneer

    auction - registered auction

    bidder - registering bidder
    client  - client of auctioneer

    noteToAuctioneer - registing note by bidder to auctioneer

    paymentMethod - associated card while registering
    bidPermissionStatus

      options: [
        "Pending",
        "Approved",
        "Declined",
        "Permanently Approved",
        "Permanently Declined",
      ]
    isShareContact
    declineReason
    permissionPrivateNote
    permissionPublicNote
    bidderScoreDuringRegistration
    bidCardNumber
    bidderPermission - amount till bidder can bid
    bidderPermissionFrom:
      options: [
        "invitationEmail",
        "auctioneerClient",
        "auction",
        "auctionRegistration",
        null,
      ]
    bidPermissionModifiedAt - only permission modified time
    bidderIPAddress - ipaddress during registration


    # Actions

    - Registration
    - Permission Update by Auctioneer


    # Registration

    Eligibility

    from auction startDate to endDate
    bidder must be successfully registered
    if auction requires verified bidder then bidder must be verfied bidder
    register to an auction only once
    bidder should have active payment method


    If not an existing client of auctioneer, bidder will automatically become new client of auctioneer. If existing then client details will be updated

    check for if bidder is invited to this auction

    getting bidcardnumber
        if a bidcard number is asigned to related client of bidder oit will be used, else next non available bid card number will be used

checking for bid permissions
from client
if not a client then from bidder score

    then auction registration will be created

    on each 15 auctions are registered the bidder score will increase

    after that email will be send
