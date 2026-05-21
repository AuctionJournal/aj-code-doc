[Auction Journal](../index.md)

# Auction expenses (inhouse)

User guide: [Auction expenses](../user_side_doc/auction/auction-expenses.md).

## Business purpose

**Inhouse auction expenses** are costs the auctioneer incurs to **conduct** the auction (marketing, supplies, cleaning, and similar). They are stored per auction while building in the CRM and support:

- A running expense log on the **Expenses** tab of **Build Auction**
- **Auction statics** API (`totalAuctionExpences` sum on `getAuctionStatics`) — shown on the auction operations screen in the user guide [Auction dashboard](../user_side_doc/auction/auction-dashboard.md)
- **Settlement invoices** — line items appear as **`auctionCharges`** on buyer/seller settlement PDFs (`settlementPDF.js` § auction charges)

Build-tab rows live in **`expencesDetails`**; settlement documents use **`auctionSettlement.auctionCharges`**. The auctioneer workflow is to record costs during build, then reflect them on settlement billing (see settlement edit: **auctionChargeCreate**).

---

## Data model

**Collection:** `expencesDetails` — `AJ-Main-Backend/app/models/expences.js`

| Field | Type | Notes |
|-------|------|--------|
| `Auctioneer` | ObjectId | Owner |
| `auctionDetailId` | ObjectId → `auctionDetails` | Parent auction |
| `expenceType` | String | UI codes `"11"`, `"12"`, `"13"` (auction / marketing / lot expense parents) |
| `account` | ObjectId → `AuctioneerMiscAccount` | Expense account (child of type parent) |
| `payForm` | ObjectId → `AuctioneerMiscAccount` | Pay-from account (`parentAccount: 0` in UI fetch) |
| `expencesDescription` | String | Description |
| `amount` | Number | **Whole dollars** (API rejects non-integer amounts) |

**Delete auction (draft):** `removeAuctionDetails` in `build-auction.js` runs `expencesDetails.deleteMany({ auctionDetailId })`.

---

## API (`app/routes/auction-expenses.js`)

| Method | Path | Handler | Role |
|--------|------|---------|------|
| POST | `/api/expenceDetails/Creation` | `expenceCreation` | Create row |
| PUT | `/api/expenceDetails/Update` | `expenceUpdate` | Update by `id` in body |
| GET | `/api/expenceDetails/:auctionDetailId` | `fetchExpenceDetails` | List for auction (populates `account`, `payForm`) |
| DELETE | `/api/expenceDetails/remove` | `removeExpenceDetails` | Delete by `id` in body |

**Controller:** `app/controllers/auctionOperations/auction-expences.js` — Joi `validateRequest` on create/update.

**Auth:** JWT + `Auctioneer` role on create/update/delete.

---

## Build UI (Expenses tab)

| Piece | Path |
|-------|------|
| Tab shell | `BuildAuction/Expenses/index.jsx` — **+ NEW EXPENSES**, table, edit/delete |
| Modal | `BuildAuction/Expenses/BuildAuctionExpense/index.jsx` — title **New/Edit Inhouse Auction Expenses** |
| API client | `src/lib/api/auction/expanse.js` |

**Type → account:** Changing `expenceType` calls `fetchCommission({ parentAccount: type })` (misc accounts API). **Pay From:** `fetchCommission({ parentAccount: 0 })`.

**Expense type options (frontend):**

| value | Label |
|-------|--------|
| `11` | Auction Expenses |
| `12` | Auction Marketing Expenses |
| `13` | Auction Lot Expenses |

---

## Settlement and invoices

| Topic | Behavior |
|-------|----------|
| **First generate** | `generateAuctionSettlement` creates buyer/seller settlements with `auctionCharges: []` (lot lines from sold lots). |
| **PDF** | `settlement/settlementPDF.js` prints `settlement.auctionCharges` (account desc, description, `$amount/100`). |
| **After generate** | `editAuctionSettlement` with `editType: auctionChargeCreate` (dashboard `BuildCharge`) adds/edits auction charges on a settlement row. |
| **Build expense total** | `getAuctionStatics` aggregates `expencesDetails` → `totalAuctionExpences` for auction statics metrics. |

**Settlement module:** [Auction settlement](./settlement/index.md) · `settlement/index.js`.

---

## Related

- [Auction build](./build.md)
- [Miscellaneous accounts](../auctioneer-misc/account.md)
- [Auction settlement](./settlement/index.md)
