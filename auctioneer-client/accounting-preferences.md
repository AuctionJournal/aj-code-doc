[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Customer buyer and consigner accounting preferences

Each **customer (client)** can have separate **Buyer** and **Consigner** accounting blocks stored in `clientsOfAuctioneer.accounting`. Settings are entered on **step 3** of the manual create/edit wizard and applied when that customer participates in **your auctions**—at registration (buyer), on **lots they consign** (seller), and during **settlement** (buyer charges).

**User walkthrough:** [Configure customer as buyer and seller](../user_side_doc/auctioneer-client/customer-preferences.md)

**Bid permission (detailed):** Documented in a separate Q&A when added; this page only references the **Bid Permission** tab briefly.

---

## Business meaning

| Block | When it applies |
|-------|-----------------|
| **Buyer** (`accounting.buyer`) | Customer registers or bids as a **purchaser** — reserved bid card, tax on purchases, buyer’s premium on won lots, bid approval limits |
| **Consigner** (`accounting.consigner`) | Customer is **seller** on lots — commission and seller tax on their consignments (or auction defaults) |

A customer can have one or both roles (`isBuyer`, `isConsigner`). Step 3 always shows **both** accordions; configure only the sections relevant to how they will be used.

**Formula sources:** Commission, seller tax, and buyer premium dropdowns load from Miscellaneous → **Formulas** (`acMiscFormulas`). See [Miscellaneous formulas](../auctioneer-misc/formulas.md).

---

## Dashboard UI

| Context | Component path |
|---------|----------------|
| Create / edit client, step 3 | `BuildClient/Accounting/index.jsx` → `BuyerAccounting.jsx`, `ConsignerAccounting.jsx` |
| Routes | `/dashboard/clients/create`, `/dashboard/clients/edit` (`BuildClient`, `activeStep === 2`) |
| View-only summary | `ViewClient/Accounting` (read-only display of saved values) |

### Buyer accordion tabs

1. **Reserved & Tax** — `reservedBidCard` (GET NEXT → `getNewReservedBidCard`), `isTaxable`, `taxExempt`, `taxExpires`
2. **Bid Permission** — `buyerPermissionStatus` (`Default` \| `Approved` \| `Declined`), amount, notes, decline reason, `isShareContact` — see [Customer-specific bid permission](./bid-permission.md)
3. **Buyers Premium** — `isDefaultBuyerPremium`, `buyerPremium` (formula id from `formulaType: "BUYER PREMIUM"`)

### Consigner accordion

- `isSpecificBuybackSetting` — `false` = use auction default commission/tax; `true` = client-specific
- When specific: `comission`, `sellerTax` (formula ids), optional `isTaxExempt` + `taxIDExpireDate`

Submit on step 3 calls `createAuctioneerClient` / `editAuctioneerClient` with full `accounting` object.

---

## Backend APIs

| Method | Path | Handler | Role |
|--------|------|---------|------|
| `POST` | `/api/client/add` | `addClient` | Create client + `accounting`; duplicate check on `accounting.buyer.reservedBidCard` |
| `PATCH` | `/api/client/:refID` | `updateClient` | Update including `accounting` |
| `GET` | `/api/client/getNewReservedBidCard` | `getNewReservedBidCard` | Next available reserved bid card number for auctioneer |
| `GET` | (misc formulas API used by UI) | `fetchMiscellaneousFormulas` | `BUYER PREMIUM`, `COMMISSION`, `SELLER TAX` options |
| `PATCH` | `/api/auctioneer/client/buyerPermissionUpdate` | `changeClientBidPermission` | Optional dedicated bid-permission update (`buyer.js`) |

Routes: `app/routes/auctioneer-clients.js` → `controllers/auctioneer/client/client.js`.

`fetchClient` populates `accounting.buyer.buyerPremium`, `accounting.consigner.comission`, `accounting.consigner.sellerTax` for display.

---

## Runtime use (where preferences matter)

### Buyer — auction registration

`auctionOperations/registration/index.js`:

- **`reservedBidCard`** — if set, used as registration **bid card number**; otherwise next free number avoiding other clients’ reserved cards and existing registrations.
- **`buyerPermissionStatus` / `buyerPermission` / notes** — see [bid permission](./bid-permission.md) and `createAuctionRegistration` mapping to `bidPermissionStatus`, `bidderPermission`, `declineReason`.

### Buyer — settlement (won lots)

`auctionOperations/settlement/index.js`:

- **`isDefaultBuyerPremium` / `buyerPremium`** — if client has specific premium formula, settlement uses it for buyer premium % and amount; else lot/auction default.
- **`isTaxable`** — when auction collects taxes, buyer tax on hammer uses lot buyer tax formula if taxable; exempt path skips buyer tax on purchases.

### Consigner — lots in an auction

`auctionOperations/lot/lot-build.js` → `getAuctionLotFields`:

- If **`isSpecificBuybackSetting`** and `comission` set → lot `commission` = client formula; else auction `Commission`.
- If specific and `sellerTax` set → lot `SellerTax` from client (unless `isTaxExempt` on consigner → not taxable).
- Drives seller-side amounts on consigned inventory through clerking/settlement.

---

## Model reference

Full field list: [Fields — Accounting block](./fields.md#accounting-block).

---

## Related

- [Building / adding a client](./build.md) — 3-step wizard overview  
- [Bidder vs client](./bidder-relationship.md) — registration uses client buyer settings  
- [Customer bid permission](./bid-permission.md) — `createAuctionRegistration` mapping  
- [Miscellaneous formulas](../auctioneer-misc/formulas.md) — define COMMISSION, BUYER PREMIUM, SELLER TAX
