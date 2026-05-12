[Auction Journal](../../index.md)

# Online bidding on an auction lot

This page describes **bidder-initiated online bidding** for timed-style auctions: placing **flat** (increment) bids or **maximum** (proxy-style) bids. Implementation is centered in `AJ-Main-Backend/app/controllers/bidding/online-timed-auction.js` and routes under `auction-lot-bidding.js`.

**Not covered here:** ring clerking and **live floor** bidding during an onsite webcast—see [Onsite live webcast bidding](../onsite-livewebcast/bidding.md).

Persisted bid history: **[Bidding fields](./fields.md)**. Lot counters updated when bids land: **[Lot fields](../fields.md)**.

---

## Business purpose

- Let an **approved registered bidder** raise the price on a **visible, open** lot during the **auction bidding window**, using the auction’s **increment schedule** and either **flat** or **maximum** bid mode.
- Keep **one bidding thread per bidder per lot**, appending steps to the `bidding` array on the `Bidding` document.
- Refresh **lot** display state (`currentBidAmount`, `nextBidAmount`, `lotWinner`, `bidCount`, etc.) and optionally **extend soft close** when a bid arrives near the end.

---

## Who may bid

- Caller must be authenticated as a **Bidder**.
- The bidder must have an **auction registration** for that auction with **`Approved`** or **`Permanently Approved`** permission (pending or declined registrations are rejected with clear messaging).
- The bidder’s **registration bid permission** (spend cap) is part of trust checks; bids that exceed policy may surface **pending / notify auctioneer** style outcomes (see response catalog in code).

---

## When bidding is allowed

- **Lot:** Must exist, not **hidden**, and not flagged as **currently live on a webcast ring** for these endpoints (`isItLiveWebcastLive`).
- **Time:** Current time must be **on or after** the auction’s **`OpenBidding`** and **on or before** the lot’s **`closeBidding`**.
- **Onsite with live webcast:** These online endpoints are only used when **pre-bidding is allowed** for that auction; otherwise the bidder is directed to the live / pre-bidding product rules.

---

## Flat bidding (`flatBidding`)

**When:** Auction is configured for **flat** bidding (`bidAmountType` is not maximum-only for this path—flat is the increment-step model).

**Rule:** The submitted **bid amount must exactly match** the platform’s **next allowed increment** from either the **start bid** (if no bids yet) or the **current high bid**, using the auction’s **`bidIncrement`** ladder.

**Behavior:**

- If the bidder is **already the lot winner** at the current price, placing another flat bid is rejected (“already winning”).
- The service records a **flat** step on the bidder’s `Bidding` row (or creates the row), updates the **lot** to the new high and **next** increment, and may trigger **soft-close extension** and **outbid notifications** to the previous winner.
- If the auction also supports **maximum bids** from others, a flat bid can be **immediately countered** by an existing max bidder so the response may be **success but you were outbid** while the lot price jumps to the proxy outcome.

---

## Maximum bidding (`maxBidding`)

**When:** Auction **`bidAmountType`** is **`maximumBidding`**. Otherwise the endpoint rejects (“max bidding not allowed”).

**Input:** Bidder sends a **ceiling** (`maxBidAmount`), in whole dollars (no cents in validation).

**Rules (high level):**

- Ceiling must be **at least** the **next required increment** above the current high bid (cannot be below the next flat step).
- If the bidder already has a max on this lot, the new ceiling must be **higher** than their previous max.
- If the bidder is **already winning**, they may **raise only their max** without changing the visible current bid (until someone else bids).

**Behavior:** The engine resolves competition between max bidders (and flat bidders), updates **`highestMaximumBidAmount`** and visible **current / next** amounts on the lot, and maintains the `bidding` history with **`maximumBidding`** / related step types. Exact competition rules live in the controller helpers (`maxBidUpdateOnMaxbid`, `newHighestBidder`, etc.).

---

## Registration and prior bid status

- **No registration** → “not yet registered” style error.
- **Previous bid on this lot** with **`acceptanceStatus`** not **Accepted** (e.g. pending or declined by auctioneer) → bidding blocked until resolved.

---

## Concurrency and UX

- Responses include **amount mismatch** / **simultaneous bidding** when the next allowed amount changed between page load and submit—bidder should refresh and bid again.
- **Emails:** Losing the winning position can queue notifications (e.g. outbid). Permission issues can notify auctioneer / bidder per template configuration.

---

## Related auctioneer actions

- **Edit bid status** (accept / pending / decline) and **raise max bid**: [Edit bidding (auctioneer)](./edit-bidding.md).
- Auctioneer **bid listings** across lots use separate read APIs (see same controller file for `auctionAuctionLotsBids` / `bidsToLotBidderWise`).

---

## Pending clarifications

- Whether **Absentee** or **sealed** reserve types use the same endpoints or a separate flow (not traced in this pass).
