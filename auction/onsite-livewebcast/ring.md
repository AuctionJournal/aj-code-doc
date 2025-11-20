[Auction Journal](../../index.md)

# Livewebcast Auction Ring

## Operations

- Prepare Ring Start
- Enter Ring
- Pause Ring
- Close Ring
- Completely Closed Ring
- Ring Room
- Upcoming Lots in ring

### Prepare Ring Start

- Can only start ring during bidding date
- Select any one ring if there are multiple rings available, if sigle ring it will be default selected
- auctioneer wont be able to enter ring in following conditions
  - if bidding date is not started
  - if ring is already live
  - if ring is completely closed instead of closed, means all lots in ring are clerked
- Select video and mic input device
- Test the input devices if necessary
- Call start ring api

### Start Ring Api

- Check is livewebcast server running
- Do these basic validations
  - auctioneer, auction, ring
  - bidding time
  - is ring completely closed
  - is ring already live
- Starting Ring for first time
  - create ring object
- Restarting ring
  - jhhj
- Update ringOptions in auction
- ring should auto close at end of the day, if not closed manually. Thus create cron job for it
- proced with entering ring

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

### Ring Room

- Each livewebcast takes plaace in a socket room
- id of the room is id of the ring
- connected user can join room with ring id
- info related to the ring is broadcasted in the room
- users joined in the room can listen to broadcasted messages

### Upcoming lots in ring

Desc : - upcoming lots in the ring will be show in public website in ring page to bidders. so that bidder can bid on those lots too, alongside live lot

- lots which fall in the ring range
- lots which are not clerked
