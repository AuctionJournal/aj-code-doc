[Auction Journal](../index.md)

# Clerking (auction lot)

## Business purpose

**Clerking** records the **auctioneer’s outcome** for each lot: **sold** (with hammer and buyer), **pass**, **hold**, or **high bid** (absentee-style), so **settlement** and **buyer/seller accounting** can run on facts, not raw bid state alone. The canonical values live on the **auction lot** (`clerkStatus`, hammer / winner fields—see [Auction lot fields](../auction-lot/fields.md)).

**Related:** [After the auction closes](./post-close.md) · [Auction lot life cycle](../auction-lot/life-cycle.md) · [Settlement](./settlement.md).

---

## Flow (how this fits with “after close”)

1. **Auction ends** — close, cache, and **who runs first** are described in **[After the auction closes](./post-close.md)** (§ **1. Auction “closed” state**).
2. **Default clerking** — lots that still have **no** `clerkStatus` get **Sold** / **Pass** / **High Bid** from bid vs reserve rules in **`lotsClerkingAfterAuctionEnd`**; lots already clerked (typical **onsite**) are left alone. Full rules: **[After the auction closes](./post-close.md)** § **2. Automatic lot clerking after close**.
3. **Editing clerking** — after the auction is closed, the auctioneer can **manually change** hammer, status, and winner where validations allow (this page, next section).

Do not duplicate the automatic rules here—keep **[After the auction closes](./post-close.md)** as the source for the batch step.

---

## Editing clerking (manual, after close)

**Product intent (as documented here):** Clerking is applied **just after the auction ends** (defaults from the batch above). **After that**, the **auctioneer can manually edit** clerking—hammer, `clerkStatus`, and winner—until business rules block further changes (e.g. payment started on the affected settlement).

**When:** Only after the auction’s **`endDate`** has passed (requests before close are rejected).

**What can change**

- **Hammer price** — must be **greater than zero** and **not below** the lot’s **current bid** (validator).
- **`clerkStatus`** — e.g. `Sold`, `Pass`, `Hold` (aligned with product / onsite usage).
- **Winner** — either an **internet bidder** or a **floor** buyer via **auctioneer client**; not both.

**Rules (high level)**

- There must be a **real change** vs what is already on the lot (identical payload is rejected).
- The **new** buyer must be **registered** on that auction with an **approved** (or permanently approved) registration; floor path follows the floor registration pattern.
- For auctions that are **not** “Onsite With Live Webcast”, if a **`Bidding`** row exists for that bidder on the lot, it must be **Accepted** (pending / declined blocks the edit).
- If **settlement was already generated** (`isSettlementGenerated`), edits **trigger updates** to buyer and seller settlement documents as needed. That settlement work runs **asynchronously**—the clerking request can return success **before** settlement totals and line items fully reflect the change (refresh if the UI must show updated figures). When a lot is no longer **`Sold`** (e.g. **Pass** / **Hold**), the **lot charge line for that lot is removed** from buyer and seller settlements—there is **no** standing zero or “voided” line kept for audit in this flow. If the **current or new buyer settlement** already has **payment started** (paid amount or receipt in a paid state), **clerking edit is blocked** for that path.

**Audit**

- **Clerk status logs:** If the lot has **no** log yet, the system can **seed** a first log from the **current** hammer / status / winner, then **append** an entry for **this** edit (including **reason** when supplied). Related **Bidding** rows may be created or updated to match the clerking outcome.

**Settlement timing:** When to **generate** settlement (hold lots, gates) is **[After the auction closes](./post-close.md)** § **3. Settlement generation**, not repeated here.

**Backend:** Post-generation settlement updates call into **`alteringSettlement`** in `AJ-Main-Backend/app/controllers/auctionOperations/clerking.js`, which reuses **`generateBuyerLotChargeInSettlement`** and **`generateSellerLotChargeInSettlement`** from the settlement controller (see **[Auction settlement § Backend reference](./settlement/index.md#backend-reference)**).

---

## Onsite live webcast

Per **[After the auction closes](./post-close.md)** § **1**, lots are often **clerked during the ring** before the auction is fully closed. The post-close batch only fills **gaps** where `clerkStatus` was never set. **Manual editing** after close follows the same rules as in **Editing clerking** above.

---

## Pending clarifications

- Whether **timed** auctions are expected to **always** get a manual clerking review or only when the auctioneer overrides auto outcomes.
