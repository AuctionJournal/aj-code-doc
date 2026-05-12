[Auction Journal](../../index.md)

# Edit bidding (auctioneer)

**Who:** **Auctioneer** only (not assistants in this handler).

**What:** After bidders place offers, the auctioneer may **change how a bid is treated** (`acceptanceStatus`) or, in a separate mode, **raise a bidder’s maximum** on a lot. Logic lives in `AJ-Main-Backend/app/controllers/bidding/auctioneer.js` (`editBidStatus`, `editMaxBid`, `acceptBid`, `pendingBid`, `declineBid`).

**Related:** [Bidding fields](./fields.md), [Online bidding](./create.md).

---

## Business purpose

- **Moderation:** Accept a bid that was **pending** (e.g. over-permission or manual review), or move a winning bid to **pending** / **declined** when policy requires it. Putting the **current winner** into **pending** restores the **visible high bid** to the **previous accepted bidder at their last accepted amount** (same idea as decline; pending means that bidder’s row is on hold while the lot shows the prior high).
- **Correction:** **Decline** the **current winning** bid step—the **high bid goes back to the previous accepted bidder at their last accepted amount** (when one exists). If there is no other accepted bidder, the lot falls back toward the **start bid** / next increment logic. The declined bidder’s row is adjusted, and they receive a **bid declined** email.
- **Max adjustment:** Under **maximum bidding**, the auctioneer may **increase** a bidder’s **ceiling** on an **accepted** bid, within **registration permission** and above the **current next bid** on the lot; the engine may run **auto-bidding** against the other high participant and change **lot winner** / price if the ceiling fight resolves that way.

---

## Entry point

- Request targets a **`Bidding`** row by **`bidId`**.
- The auction must belong to the **logged-in auctioneer**; otherwise **unauthorized**.

---

## Changing `acceptanceStatus`

**Guardrails**

- If the new status is the **same** as the current one → rejected (“already …”).
- For **`Pending`** or **`Declined`**, the bid row must belong to the **current lot winner**. You cannot put a **losing** bidder’s row into pending/declined through this path.

**Accepted**

- Marks the bid **Accepted**.
- **Clearing a hold:** If the row was **Pending**, accepting it **applies that bidder’s bid to the lot** only when their **latest bid amount is higher than the lot’s current high**—then they become **lot winner** at that amount and **next bid** is recomputed. If their latest amount is **not** above the current high, status still becomes Accepted but the **visible high/winner** may stay **unchanged** (implementation matches `acceptBid` in `auctioneer.js`).
- **Pending product copy:** Whether customer-facing text should always say “accepting applies their bid” without the amount condition is **undecided**—confirm with operations/legal if needed.

**Pending**

- **Customer-facing outcome:** Same as for **decline** for the **lot display**—the **high bid goes back to the previous accepted bidder at their last accepted amount** (when one exists); otherwise the lot is driven from **start bid** / increment logic as in code.
- **System steps:** Marks this bidder’s row **Pending** and **recomputes** the lot winner, current amount, next increment, and max fields from that **other accepted** bidder (flat vs **auto/max** steps differ when reading the “previous” winning amount from history).

**Declined**

- **Customer-facing outcome:** The **high bid returns to the previous accepted bidder at their last accepted amount** whenever that bidder exists; otherwise the platform falls back to **start bid** (and recomputed next increment) as appropriate.
- **System steps:** **Removes** the latest bid step from the declined bidder’s history (pop), may lower their stored **max** to match the new last step, marks the row **Declined**, updates the **lot** winner and amounts, may decrement **bid count**, and sends a **bid declined** email to the declined bidder.

---

## Editing maximum bid (`editType`: max-bid-amount)

Used when the auction uses **maximum bidding** and the auctioneer needs to **raise** a bidder’s ceiling on an existing **accepted** bid.

**Checks**

- Bidder’s **auction registration** must be in an **Approved** (including permanently approved) state.
- The **`Bidding`** row must already be **Accepted**.
- New **max** must be **greater** than the bidder’s **current max** on that lot.
- New **max** must **not exceed** the bidder’s **registration bid permission**.
- New **max** must be **at least** the lot’s **next bid** amount (cannot be below the public next step).

**Outcomes**

- If this bidder is **already the lot winner**, the stored max (and lot **highestMaximumBidAmount**) is updated; visible current price may stay the same until someone else bids.
- If they are **not** the winner, the service runs **automatic bidding** against the current winner’s max and may change **current bid**, **next bid**, **winner**, and **bid count** depending on who wins the proxy fight.

---

## What this doc does not spell out

- Exact **increment math** and edge cases inside `automaticBidding` / `auctionLotBidEdit`—treat as implementation detail unless you need runbook precision.
- **Listing** bids (`bidsToLotBidderWise`, `auctionAuctionLotsBids`)—separate read APIs for dashboards.

---

## Pending clarifications

- Whether **assistants** should ever perform equivalent actions in product policy (this API is **auctioneer-only** in code).
- **Simpler messaging** for “accept pending bid” vs the rule that promotion to high bidder happens only when **latest amount is greater than current high**.
