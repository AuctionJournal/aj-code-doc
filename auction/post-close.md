[Auction Journal](../index.md)

# After the auction closes

This page describes the **business flow** once an auction is **finished** (timed catalog complete, or onsite rings and schedule resolved): system **close**, **lot clerking** defaults, **cache** cleanup, and **settlement** generation. Grounded in `AJ-Main-Backend` (`auction-events.js`, `auction-lot/actions/clerking.js`, settlement generation).

**Related:** [Clerking](./clerking.md), [Settlement](./settlement.md) · [Generate settlement](./settlement/create.md), [Auction lot life cycle](../auction-lot/life-cycle.md).

---

## 1. Auction “closed” state

When the platform runs **`auctionEnd`** for an auction (scheduled job, or a path that explicitly ends the auction—for example some onsite/settlement flows):

- **`Status`** on the auction is set to **`Closed`** (if it was not already).
- **Caches** tied to the auction and its lots are **cleared** (Redis: closing schedule, summary, active-auction set, per-lot hashes) so listings and live views stop treating it as active.
- **Post-close clerking** runs on lots (see below).

**Onsite live webcast** auctions often already have **per-lot clerk outcomes** (`Sold`, `Pass`, `Hold`, etc.) from the ring. The batch step mainly affects lots that still have **no clerk status** and need a default outcome from bid vs reserve rules.

---

## 2. Automatic lot clerking after close (`lotsClerkingAfterAuctionEnd`)

This step sets **`isAuctionClosed: true`** on affected lots and assigns **`clerkStatus`** where it is still unset (`null` / missing), separately for “winning” vs “not sold” patterns.

**Timed / normal path (not Absentee Bidding)**

- **Sold:** Visible lot (`isHidden` not true), **has a positive current bid**, bid **meets or beats reserve**, and **no clerk status was set yet** → **`clerkStatus: Sold`**.
- **Pass:** Still no clerk status, and the lot is **hidden**, or **no real bid**, or bid **below reserve** → **`clerkStatus: Pass`**.

**Absentee Bidding**

- Eligible lots with a **positive high bid** (and not hidden) get **`clerkStatus: High Bid`** instead of `Sold` when applying the “winning” bucket—reflecting absentee semantics in code.

Lots that **already** have a clerk decision from the auctioneer/clerk UI are **not** overwritten by these filters (they require missing `clerkStatus`).

---

## 3. Settlement generation (auctioneer)

Settlement is **not** assumed to run automatically on every auction the instant it closes; the auctioneer (or product flow) **generates** it when rules allow.

**Typical gates**

- Auction is **published**.
- **`endDate`** has passed (or the auction is otherwise treated as closed for generation—**onsite** flows may also require **all rings completely closed** before forcing end/settlement).
- **`isSettlementGenerated`** is still **false** (only one generation per auction).
- **Hold:** If any lot is still **`clerkStatus: Hold`**, generation is **blocked** unless the auctioneer explicitly chooses to **ignore** hold lots; ignoring may also move held lots to **`Pass`** in a follow-up update in code.

**What gets built**

- **Buyer-side** settlement lines for **approved** registrations, from **lots that count as sold** for that bidder (rules differ slightly for **onsite live webcast** vs online timed—e.g. winner vs clerk `Sold`).
- **Seller-side** settlement lines for **distinct sellers** on lots in the auction.
- Auction flag **`isSettlementGenerated`** is set **true** after a successful generation run, **including** when **zero** settlement documents are inserted (no sold lots to bill).

Detail of line items, premiums, taxes, and edits: **[Auction settlement](./settlement.md)** · [Generate settlement](./settlement/create.md) · [Settlement fields](./settlement/fields.md).

---

## 4. What happens next (product-level)

- **Invoices / PDFs / email** and **receipts** are part of the settlement and ops story (see **[Auction settlement](./settlement.md)**).
- **Payments** and **payouts** (e.g. Stripe) are documented under **[Payment](../payment/index.md)** when you extend that module.

---

## Pending clarifications

- Exact **cron** or **queue** names and schedules that call **`auctionEnd`** vs per-lot close (timed soft close)—operational runbook detail.
- Whether **`AccounType`** vs **`AuctionType`** in an onsite settlement branch is a **bug** only; doc uses **business type** (onsite live webcast).
