[Auction Journal](../../README.md)

# Generate Auction Settlement

# Eligiblity

- Auction must be closed
- Settlement should not be gereated before
- Holded lots should not be there, else it should be ignored

## Generate Buyers Settlement

Fetch all approved auction registrations

> ### Single Buyer Settlement
>
> Fetch all lot biddings by bidder and sort it by lotNumber
>
> ---
>
> #### Auction Charges of Buyer Settlement
>
> It will be initially empty later auctioneer can add/edit it
>
> ---
>
> #### Lot Charges of Buyer settlement
>
> Loop through each lot bidding by bidder
>
> > Single Lot Charge
> >
> > - check weather bidder is winner of the lot and lot is clerked as sold if yes then
> > - hammerPrice become currentBid Amount
> > - check for default buyer premium
> > - if no default buyer premium add lot's buyer premium
> > - check for taxability in auction and for client
> > - if taxable buyer tax defined in lot is added

## Generate Sellers Settlement

Fetch all unique sellers in auction from auction lot
