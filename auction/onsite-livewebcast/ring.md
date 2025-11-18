[Auction Journal](../../index.md)

# Livewebcast Auction Ring

## Operations

- Start Ring
- Enter Ring
- Pause Ring
- Close Ring
- Completely Closed Ring

### Start Ring

- Can only start ring during bidding date
- Select ring if there are multiple rings available
- Select video and mic input device
- Test the input device if necessary
- Call start ring api

### Start Ring Api

- Check is livewebcast server running
- Do these basic validations
  - auctioneer, auction, ring
  - bidding time
  - is ring completely closed
  - is ring already live
- Create Ring Object

### Enter Ring

- Connect to socket
- join Room
- Play ring

### Pause Ring

- Pause ring
- change state to paused
- when pause noboday cant bid or do any other operation in ring

### Close Ring

- Close the ring
- remove current lot from live state
- close video streaming
- check if all lots in ring are clerked, if yes make ring as completely closed

### Completely Closed Ring

- If all lots in the ring are clerked, ring become completely closed
- Once Completely closed, this ring cant be opened
- Once all lots in auction are clerked .....
