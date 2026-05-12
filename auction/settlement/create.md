[Auction Journal](../../index.md)

# Generate auction settlement

Business context and gates: **[Auction settlement](./index.md)** · [After the auction closes](../post-close.md) § **3**.

**Backend:** `AJ-Main-Backend/app/controllers/auctionOperations/settlement/index.js` — **`generateAuctionSettlement`** orchestrates buyer/seller loops; each sold lot’s line object is built by **`generateBuyerLotChargeInSettlement`** or **`generateSellerLotChargeInSettlement`** (same helpers **`clerking`** reuses for settlement ripple). Full table: [Auction settlement § Backend reference](./index.md#backend-reference).

---

## Eligibility (summary)

- Auction **published**, **`endDate`** has passed, **`isSettlementGenerated`** is false.
- **Hold** lots block generation unless the auctioneer chooses **ignore hold lots** (held lots may then be updated to **Pass**).
- Invalid early attempts return a generic **invalid generation** response from the API.

---

## Buyer settlement

1. Load **approved** registrations (**Approved** or **Permanently Approved**).
2. For **each** registration, build **at most one** buyer settlement for that auction: all qualifying sold lots for that buyer share the **same** settlement row (`lotCharges` array grows with each lot)—**floor** and **internet** registrations alike (no per-ring split). Those are **separate registration types**; see [Registration](../registration.md) § **Floor vs online bidder (identity)** (conversion flow **pending**).
3. For each registration, resolve **biddings / won lots** (path depends on **auction type**):
   - **Onsite with live webcast** — **Floor:** lots with **`winningFloorBidder`** matching that registration’s client and **`clerkStatus: Sold`**. **Internet:** live webcast rows for that bidder whose populated lot is **`Sold`**.
   - **Other auctions** — **`LotBidding`** for that bidder, lot must be **`Sold`** and **`lotWinner`** must match the bidder.
4. Sort by **lot number**.
5. For each qualifying lot, build a **lot charge** (hammer from lot, **buyer premium**, **buyer tax** when the auction collects tax—see lot/client formulas).
6. **Auction charges** on the buyer settlement document start **empty**; the auctioneer can add or edit later.
7. If the buyer has **no** sold lots, **no** buyer settlement row is created for that registration.

---

## Seller settlement

1. Collect **distinct sellers** from lots in the auction.
2. For each seller, load their lots; add **lot charges** only for lots with **`clerkStatus: Sold`** (commission, seller tax, extras per lot).
3. If the seller has **no** sold lots, **no** seller settlement row is created.

---

## Completion

- All new rows are inserted in one batch (the batch may be **empty**); the auction is still updated with **`isSettlementGenerated: true`**.
- **Invoice numbers** are assigned per settlement row when rows exist (auctioneer ticker + random component in current implementation).
- If clerking changes later, settlement values are updated through the **clerking update flow** (not by re-running first-time generation). That update is **asynchronous** relative to the clerking API response.
- If clerking removes **Sold** from a lot, that lot’s **lot charge** rows are **removed** from buyer and seller settlements (no zero/void placeholder line).
