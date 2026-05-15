[Auction Journal](../index.md) · [Auctioneer Miscellaneous](index.md)

# Miscellaneous accounts (chart of accounts)

Auctioneers maintain a **chart of accounts** under Miscellaneous → **Account**. Each auctioneer gets **predefined parent account types** (ASSET, LIABILITY, INCOME, EXPENSE) and can add **sub-accounts** under a parent. Sub-account numbers are **auto-generated** by the backend when a parent is selected.

These accounts are used throughout **auction operations**—expenses, lot accounting, settlement charges/adjustments, shipping, and similar flows where an account must be chosen.

**User-facing steps:** [`user_side_doc/auctioneer-misc/account.md`](../user_side_doc/auctioneer-misc/account.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Parent accounts** | 15 fixed parents (e.g. `1000` Money in or out, `4500` Buyer's premium). Defined in `mainAccounts` (frontend `data.js`, backend `mainAccounts.js`). |
| **Account types** | `ASSET`, `LIABILITY`, `INCOME`, `EXPENSE` — one type per parent. |
| **Sub-account numbers** | Must fall in range `(parentAccNo, parentAccNo + 500)` exclusive upper logic in validation: `accNo > parent.accNo && accNo < parent.accNo + 500`. |
| **Auto number** | `GET/POST` `getAccountNo` returns next available number under parent (last used + 1, or `parentAccNo + 1` if none). |
| **Description** | Required; must be unique per auctioneer + parent. |
| **Adjustments parent** | `parentAccount === 10` (`6000 : Adjustments`) — optional `usedFor`: `Discounts` or `Surcharges`. |
| **Defaults on signup** | `createDefaultMiscellaneousAccounts(auctioneerId)` seeds sub-accounts (e.g. 1001 Check written, 1002 Cash under parent 0). |
| **Edit** | Description and `usedFor` only; parent and account number locked in UI. |
| **Delete** | By account `_id`; default seeded accounts can be deleted unless restricted elsewhere. |

---

## Data model

**Collection:** `AuctioneerMiscAccount` (`app/models/auctioneerMiscAccounts.js`)

| Field | Type | Notes |
|-------|------|--------|
| `auctioneer` | ObjectId | Owner |
| `parentAccount` | Number | Index into `mainAccounts` (`id` 0–14) |
| `accNo` | Number | Unique per auctioneer (index) |
| `desc` | String | Description |
| `usedFor` | enum | `Discounts` \| `Surcharges` — adjustments parent only |
| `isDefault` | Boolean | Seeded accounts |

---

## Dashboard UI

**Route:** `/dashboard/miscellaneous/account`  
**Page:** `Pages/Miscellaneous/account.page.jsx`  
**Components:** `Components/Miscellaneous/Account/`

| Piece | Role |
|-------|------|
| `index.jsx` | Lists 15 parent rows (accordion); search/filter by type; **Add new Account** |
| `BuildAccount/index.jsx` | Dialog **New Account** — parent selector, read-only account number, description |
| `SingleAccount/index.jsx` | Loads sub-accounts when row expanded; edit/delete |
| `data.js` | `mainAccounts` labels for UI (`value` = parent id) |

### Create flow

1. User selects parent in dropdown (UI label `*Seller Tax` — options are parent lines like `1000 : Money in or out`).
2. `getAccountNo({ auctioneer, parentAccount })` → fills `accNo` (read-only).
3. User enters `desc` (+ `usedFor` if parent is Adjustments).
4. `POST /api/miscellaneous/addAccount` with `{ parentAccount, accNo, desc, usedFor? }`.

### List flow

- Parents shown from static `mainAccounts` (not API).
- Expanding a row calls `POST /api/miscellaneous/accounts` with `{ auctioneer, parentAccount }` to load sub-accounts.

---

## Backend APIs

**Routes:** `app/routes/auctioneer-mscaccounts.js`  
**Controller:** `app/controllers/auctioneer/miscellaneous/accounts/subAccount.js`

| Method | Path | Auth | Purpose |
|--------|------|------|---------|
| POST | `/api/miscellaneous/getAccountNo/:auctioneer/:parentAccount` | Yes | Next `accNo` for parent |
| POST | `/api/miscellaneous/addAccount` | Yes | Create sub-account |
| POST | `/api/miscellaneous/accounts` | Yes | List (optional `parentAccount` in body) |
| GET | `/api/miscellaneous/account/:refID` | Yes | Single account |
| PATCH | `/api/miscellaneous/account/:refID` | Yes | Update `desc`, `usedFor` |
| DELETE | `/api/miscellaneous/account/:refID` | Yes | Delete sub-account |

**Frontend client:** `lib/api/miscellaneous/accounts.js`, `getAccountNo` in `lib/api/miscellaneous/invoice.js`.

---

## Parent account reference

| id | accNo | Type | Description |
|----|-------|------|-------------|
| 0 | 1000 | ASSET | Money in or out |
| 1 | 1500 | ASSET | Accounts receivable |
| 2 | 2000 | LIABILITY | Accounts payable |
| 3 | 2500 | LIABILITY | Sales tax payable |
| 4 | 3000 | LIABILITY | Seller auction charges |
| 5 | 3500 | INCOME | Seller lot charges |
| 6 | 4000 | INCOME | Buyer auction charges |
| 7 | 4500 | INCOME | Buyer's premium |
| 8 | 5000 | INCOME | Buyer lot charges |
| 9 | 5500 | INCOME | Auctioneer commission |
| 10 | 6000 | INCOME | Adjustments |
| 11 | 6500 | EXPENSE | Auction expenses |
| 12 | 7000 | EXPENSE | Auction marketing expenses |
| 13 | 7500 | EXPENSE | Auction lot expenses |
| 14 | 8000 | EXPENSE | Salesperson commission |

---

## Where accounts are consumed

Auctioneer dashboard loads accounts via `fetchCommission` / `fetchMiscAccounts` when picking accounts in:

- Auction expenses (`BuildAuctionExpense`)
- Lot accounting (`LotAccounting`)
- Settlement charges/receipts (`BuildCharge`, `AddReceipt`)
- Shipping (auction build)
- Formula build (commission linkage)

---

## Related documentation

- [Auctioneer Miscellaneous index](index.md)
- [Auction build](../auction/build.md)
- [Auction settlement](../auction/settlement.md)
