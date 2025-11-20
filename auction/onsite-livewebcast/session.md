[Auction Journal](../../index.md)

# User Sesion

## Operations

- Auctioneer session
- Ring session
- Bidder session
- Public session

### User Session

- possible users are auctioneers, bidders, and public users
- auctioneers and bidders connect to socket server with auth token
- public users connect without auth token
- on user connection. socket server call main backend with token to verify and get user details
- each user get socket id
- if verified user, user details and provided token will be stored in socket
- then connected to socket
- After successfull connection, user join room related to ring
- user count in room will be increased
- User will get update in ring or can send update to ring
- user can leave room or diconnect socket any time
- user count in room will be decreased

### Actioneer Session

- auctioneer connect to socket
- then join room related to the ring
- fetch the related ring details
- mark the ring as live and isAuctioneerDisconnected false
- if isAuctioneerDisconnected was previously true then mark paused as false
- update ring datas with above details
- stop all related cron jobs to close ring on auctioneer disconnect
- associated ring details will be attached to auctioneer socket
- Auctioneer can now do all operations
- Auctioneer leaving room or disconnecting socket

  - On following situations this could happen. stop ring by auctioneer, network problem, browser or browser tab closed or inactive for long time
  - Action taken on disconnect
    - task1:- After 4 sec of disconnect. ring will not be live, will be paused and isAuctioneerDisconnected will be true
    - task2:- After 1 min, api will be called to update ring status and close unused ring properly
    - ring deltails from socket will be removed
  - On Auctioneer reconnect

    - if reconnected with 4 sec, all disconnect actions will not be executed and everything goes as previously
    - xxx

### Bidder Session

- only work in aj public website
- Same flow as user session

### Public User Session

- only work in aj public website
- Same flow as user session
