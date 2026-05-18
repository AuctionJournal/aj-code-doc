[Auction Journal](../index.md) · [Auctioneer Miscellaneous](index.md)

# Miscellaneous formulas

Formulas are reusable **calculation rules** an auctioneer defines under Miscellaneous → **Formulas**. They drive how Auction Journal computes **buyer premium**, **taxes**, **commissions**, and related amounts during **auctions**—clerking, lot accounting, settlement, and default lot settings.

**User-facing guide:** [`user_side_doc/auctioneer-misc/formulas.md`](../user_side_doc/auctioneer-misc/formulas.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Scope** | Per auctioneer (`auctioneer` on `acMiscFormulas`). |
| **Formula types** | `BUYER PREMIUM`, `BUYER TAX`, `BUYER LOT CHARGE`, `COMMISSION`, `SELLER TAX`, `SALESPERSON COMMISSION` (UI may hide BUYER LOT CHARGE). |
| **Clerk status** | `Sold` or `Passed` — when the formula applies in clerking context. |
| **Account** | Links to an `AuctioneerMiscAccount` sub-account; options filtered by formula type’s parent account (see `relParentAccount` in UI). |
| **Flat calculation** | Single `calculation` (%). Applied as % of **hammer price** (or **auctioneer’s commission** for salesperson commission). |
| **Sliding scale** | **COMMISSION** only. `commissionType: "Sliding Scale Commission"` with `calculations[]`: `{ percentage, uptoHammerPrice }` tiers in **ascending** `uptoHammerPrice` order. |
| **Auction create gate** | Draft auction requires at least one formula each of: COMMISSION, BUYER PREMIUM, BUYER TAX, SELLER TAX (`auctioneerEligiblityToCreateAuction`). |
| **Settlement** | Sliding scale: walks tiers until hammer (cents) ≤ `uptoHammerPrice * 100`, uses that tier’s `percentage` for commission. |

---

## Data model

**Model:** `acMiscFormulas` (`app/models/acMiscFormulas.js`)

| Field | Notes |
|-------|--------|
| `formulaType` | Enum (see above) |
| `formulaCode` | Short code (searchable) |
| `formulaDesc` | Description |
| `clerkStatus` | `Sold` \| `Passed` |
| `account` | ObjectId → `AuctioneerMiscAccount` |
| `commissionType` | `Flat Commission` \| `Sliding Scale Commission` \| null |
| `calculation` | Number (%) for flat |
| `calculations` | Array of `{ percentage, uptoHammerPrice }` for sliding scale |
| `calculationType` | `Hammer Price` \| `Auctioneer’s Commision` (schema; UI uses formula type for salesperson label) |

---

## Formula type → parent account (UI)

From `BuildFormula/index.jsx` / backend `formulaTypes`:

| Formula type | Parent account id | Typical parent |
|--------------|-------------------|----------------|
| BUYER PREMIUM | 7 | 4500 Buyer's premium |
| BUYER TAX | 3 | 2500 Sales tax payable |
| COMMISSION | 9 | 5500 Auctioneer commission |
| SELLER TAX | 3 | 2500 Sales tax payable |
| SALESPERSON COMMISSION | 14 | 8000 Salesperson commission |

Account dropdown loads sub-accounts via `POST /api/miscellaneous/accounts` with `parentAccount`.

---

## Dashboard UI

| Item | Value |
|------|--------|
| Route | `/dashboard/miscellaneous/formulas` |
| Page | `Pages/Miscellaneous/formulas.page.jsx` |
| List | `Components/Miscellaneous/Formulas/index.jsx` |
| Create/edit | `Components/Miscellaneous/Formulas/BuildFormula/index.jsx` |

### Create / edit flow

1. **Formula Type**, **Formula Code**, **Description**, **Clerk Status**, **Account**.
2. If type is **COMMISSION**: choose **Flat commission** or **Sliding scale commission**.
3. **Flat:** enter % + label “X Hammer Price”.
4. **Sliding scale:** one or more rows — **Calculation** (%) × **Upto hammer price** ($); **ADD MORE** for extra tiers; validation: 0–100%, prices ascending.
5. `POST /api/miscellaneous/formula/create` or `PATCH .../formula/:refID`.

### List display

- Flat: `(10%) of Hammer price` (or Auctioneer’s commission for salesperson).
- Sliding scale: info icon tooltip listing each tier, e.g. `15% upto $1000`.

---

## Backend APIs

**Routes:** `app/routes/auctioneer-formulas.js`  
**Controller:** `app/controllers/auctioneer/miscellaneous/formulas.js`  
**Role:** `requireAuth` + `allowRoles("Auctioneer")` (except `GET` single formula).

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/api/miscellaneous/formula/create` | Create formula |
| POST | `/api/miscellaneous/formulas` | List (body: `searchTerm`, `formulaType[]`, `clerkStatus[]`) |
| GET | `/api/miscellaneous/formula/:refID` | Single (populates `account`) |
| PATCH | `/api/miscellaneous/formula/:refID` | Update |
| DELETE | `/api/miscellaneous/formula/:refID` | Delete |

**Frontend:** `lib/api/miscellaneous/formulas.js`

---

## Where formulas are used

- **Auction build** — eligibility requires core formula types.
- **New lot defaults**, **lot accounting**, **settlement** charges, **client/floor bidder accounting**, **auction expenses** — pick formulas from lists filtered by type.
- **Settlement engine** — applies flat or sliding commission logic on hammer price.

---

## Sliding scale (implementation note)

Settlement (`auctionOperations/settlement/index.js`): for each tier in order, set `commissionPercentage` from tier; **break** when `hammerPrice <= uptoHammerPrice * 100` (hammer stored in cents). Ensures tier bands by maximum hammer threshold.

---

## Related documentation

- [Miscellaneous accounts](account.md)
- [Auction build](../auction/build.md)
- [Auction settlement](../auction/settlement.md)
