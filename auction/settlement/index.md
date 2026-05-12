[Auction Journal](../../index.md)

# Auction settlement

## Business purpose

**Settlement** turns **closed auctions** and **clerking outcomes** into **buyer** and **seller** financial documents: one settlement row per **buyer** (or floor buyer registration) who actually has **sold lots** to pay for, and one per **distinct seller** who has **sold lots** to be paid for. After generation, the auction is marked **settlement generated**; **clerking edits** and **settlement edits** then follow separate rules (see [Clerking](../clerking.md), [Generate settlement](./create.md)).

**Related:** [After the auction closes](../post-close.md) § **3** (timing next to close and clerking) · [Clerking](../clerking.md) · [Payment](../../payment/index.md).

---

## Who runs it

The **auctioneer** (or a product flow acting for them) **generates** settlement **after** the auction has **ended** for billing purposes. It does **not** run automatically the moment the catalog closes; the auctioneer chooses when to generate, subject to eligibility below.

---

## Eligibility (when generation is allowed)

These gates match the intended product flow (see backend `generateAuctionSettlement` and [After the auction closes](../post-close.md) § **3**):

| Gate | Meaning |
|------|--------|
| **Published** | The auction must be **published**. |
| **Ended** | Clock time must be **on or after** the auction **`endDate`** (invalid if still before end). |
| **Not already generated** | **`isSettlementGenerated`** must still be **false**—only **one** generation per auction. |
| **Hold lots** | If any lot is still **`clerkStatus: Hold`**, generation is **blocked** unless the auctioneer opts to **ignore hold lots**. If ignored, held lots may be moved to **`Pass`** in a follow-up update so settlement can proceed. |

**Onsite live webcast:** The end-to-end close story may also depend on **rings** and explicit **auction end**; see [After the auction closes](../post-close.md) § **1** and § **3** for how onsite timing differs from a simple timed **`endDate`**.

---

## What gets created

- **Buyer settlements** — Built from **approved** auction registrations (**Approved** or **Permanently Approved**). **One buyer settlement per registration per auction** (not split across multiple settlement rows)—same rule for **internet bidders** and **floor bidders** (client registration): every sold lot for that registration becomes a **`lotCharges`** line on the **same** document, with **one** **`auctionCharges`** block and **one** **`adjustment`** for that invoice (not separate invoices per ring or session). Floor and online paths are **different account models** and are **not** combined on one registration; see [Registration](../registration.md) § **Floor vs online bidder (identity)** (floor→online conversion and cleanup are **not implemented yet**—**Pending clarifications** on that page). **Auction-level charges** on the settlement start **empty**; the auctioneer can add or edit them later. Registrations with **no** qualifying sold lots **do not** get a settlement row (nothing to invoice).
- **Seller settlements** — One row per **seller** (auctioneer client) who has lots in the auction; **lot charge** lines include only lots **`clerkStatus: Sold`** (commission, seller tax, etc., per lot setup).

**Sold vs winner:** For **online / timed** style auctions, a lot generally must be **`clerkStatus: Sold`** **and** the registration’s bidder must be the lot’s **`lotWinner`**. For **onsite with live webcast**, buyer lines use the **live / floor** winning patterns while still requiring the lot to be clerked **Sold** (detail in [Generate settlement](./create.md)).

After a **successful** generation run, the auction flag **`isSettlementGenerated`** is set **true**—even when **no** buyer or seller settlement rows are created (e.g. every lot is **Pass** / not sold, so nobody gets an invoice). In that case there is still **no second “first” generation**, so **clerking** (sold lots, winners) should be correct **before** running generate when invoices are expected.

---

## After generation

- **Invoices / PDFs / email** and **receipts** are part of ongoing ops (placeholders in this doc set until those pages exist).
- **Payments** tie to settlement balances—see **[Payment](../../payment/index.md)**.
- **Editing clerking** after settlement exists is the supported way to update settlement amounts; clerking changes **alter existing settlements** when allowed (no separate regenerate path). Settlement changes from clerking are applied **asynchronously**—do not assume invoice totals update in the same moment as the clerking success response.
- If clerking takes a lot **out of Sold** (**Pass**, **Hold**, etc.), the **lot’s settlement line items are dropped** from buyer and seller documents (not retained as zero or void rows for audit in this path). **Clerk status logs** on the lot remain the paper trail for clerking history.
- **Payment already started** on a buyer settlement can **block** further clerking changes—see [Clerking](../clerking.md).

---

## Settlement edits (auctioneer, manual)

Separate from clerking: the auctioneer can change settlement documents **directly** where the API allows (e.g. **auction-level charges**, **lot charge** fields, **extra lot charges**, deletions—many branches behind one handler). **Receipt status Paid** blocks edits. **Adjustments** (single discount/surcharge line tied to a misc account) are set or removed via dedicated handlers. See **Backend reference** below for function names.

---

## Backend reference

Primary module: `AJ-Main-Backend/app/controllers/auctionOperations/settlement/index.js`. HTTP wiring: `AJ-Main-Backend/app/routes/auction-settlement.js` (e.g. `generateSettlement`, `editSettlement`, `settlementAdjustment`).

| Function | Role |
|----------|------|
| **`generateBuyerLotChargeInSettlement`** | Builds one **buyer** `lotCharges` payload from a sold lot (hammer from `currentBidAmount` or optional override, buyer premium, buyer tax when applicable, `totalAmount`). Used by **`generateAuctionSettlement`** and imported by **`clerking`** (`alteringSettlement`) to rebuild lines after clerking. |
| **`generateSellerLotChargeInSettlement`** | Builds one **seller** `lotCharges` payload (hammer, commission including sliding scale, seller tax, seller extra expenses, net `totalAmount`). Same reuse from **`generateAuctionSettlement`** and **`clerking`**. |
| **`generateAuctionSettlement`** | Runs eligibility, loads registrations and sellers, loops lots, calls the two generators above, **`insertMany`** settlements, sets auction **`isSettlementGenerated`**. |
| **`editAuctionSettlement`** | Patches an existing settlement by `editType` (e.g. create/edit **auction** charges, edit **lot** charge entries, delete charge rows). Rejects if **`reciptStatus === Paid`**. |
| **`auctionSettlementAdjustment`** | Applies one **adjustment** object (fixed or % of `totalAmount`), sets **`grandTotalAmount`**, validates against **totalPaidAmount**. |
| **`deleteAuctionSettlementAdjustment`** | Clears **adjustment** and resets **grandTotalAmount** to **totalAmount**. |

**Clerking:** `AJ-Main-Backend/app/controllers/auctionOperations/clerking.js` imports `generateBuyerLotChargeInSettlement` and `generateSellerLotChargeInSettlement` from `./settlement` (same module as above) for post-generation settlement updates.

---

## Doc map

| Topic | Page |
|--------|------|
| Line-item build (buyer/seller loops, premiums, taxes) | [Generate settlement](./create.md) |
| Schema-style field groups | [Fields](./fields.md) |
| Close, cache, default clerking | [After the auction closes](../post-close.md) |

**Pending:** Deep doc for each **`editAuctionSettlement`** `editType`, email settlement, add receipt.
