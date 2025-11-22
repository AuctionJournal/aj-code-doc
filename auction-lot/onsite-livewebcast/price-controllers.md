[Auction Journal](../../index.md)

# Price Controllers

Price controllers are provided to auctioneers during livewebcast. So that auctioneer can control the price of the lot opened.

- Price can be controlled either by clicking any price button or by editing custom price.
- working reference from hibid livewebcast playground

# Following Price controllers are available

- Floor Bid
- Set Asking price (next bid amount)
- Set Bid increment

# Bid Increment controllers

## Defenition

- This controller can be used to set static increment value for lot
- If bid increment is set to x. Then increment will happen in the multiple of x
- custom increment can be set from edit popup
- or can be set by increment buttons
  - base increment buttons are [1, 3, 5, 10, 25, 50, 100, 250, 500, 1000]
  - base increments are multiplied by 10, to get next set of increments
  - base increments are divided by 10, to get previos set of increments
    - Examples
      - [0.01, 0.03, 0.05, 0.10, 0.25, 0.50, 1.00, 2.50, 5.00, 10.00] = base/100
      - [0.1, 0.3, 0.5, 1, 2.5, 5.0, 10.0, 25.0, 50.0, 100.0] = base/10
      - [1, 3, 5, 10, 25, 50, 100, 250, 500, 1000] = base
      - [10, 30, 50, 100, 250, 500, 1000, 2500, 5000, 10000] = base \* 10

## Changes after bid increment updation

- once set, bid increment based on **auction bid increment(auto bid increment)** will be **disable** and will happen according to specified bid increment from this controller.
- once set asking price controller and floor bid controller will be updated according to increment multiple

## Next bid amount calculation on setting static bid increment

- Next bid amount should fall under multiples of mentioned bid increment
- Thus multiples of x are x, 2x, 3x, 5x....
- If current bid amount + x fall under this. then next amount will be current bid amount + x
- If not next amount will be, next possible amount. Thus next bid amount = current bid amout + difference + x
- Possible Scenerio 1
  - current bid amount = **20**
  - auctioneer set a static bid increment = 5
  - multiple of 5's are ....15,20,25,30
  - Thus next bid amount = **25**
- Possible Scenerio 2
  - current bid amount = **23**
  - auctioneer set a static bid increment = 5
  - multiple of 5's are ....15,20,25,30
  - Thus next bid amount will not be 25 or 28
  - It will be = **30** (same flow hibid)
