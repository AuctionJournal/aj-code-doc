[Auction Journal](../index.md)

# Clerking

Clerking details will be stored in auction lot itself

# Operations

## Initial Auto Clerking

after auction close clerking will take place automatically

## Clerking Edit

Clerking edit on an auction lot

- can edit hammer price, clerk status, lot winner

- if settlement is already generate need to edit that also

- check if auction is closed

- if exitsing winner paid any amount the clerking cant be edited

- there should be some change to edit

- hammer price should be more than current bid
- new bidder must be registered on the auction and approved
- new bidder on lot should not be disabled
- clerk edit log
  - if no existing clerk log, create the fisrt log of orginal clerk
  - create a clerk log of current edit
- create related bidding
- lot update
- altering settlement if already generated
  - alter buyer settlement
  - check the type of buyer settlement alteration there are 7 types
    - isBSCreate
    - isBSAddLotChargeSame
    - isBSAddLotChargeNew
    - isBSUpdateLotCharge
    - isBSTransferLotCharge
    - isBSRemoveLotCharge
    - isBSDeleteEntire
  - alter seller settlement
  - check the type of seller settlement alteration there are 5 types
    - isSSCreate
    - isSSAddLotCharge
    - isSSUpdateLotCharge
    - isSSRemoveLotCharge
    - isSSDeleteEntire

### Making prescedense bidder as winner

## Clerk status logs
