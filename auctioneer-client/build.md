[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Building / adding a client (manual create)

Manual client creation is the auctioneer’s primary way to add a **customer** record before or between auctions. Data is stored in `clientsOfAuctioneer` and scoped to the signed-in auctioneer.

**User walkthrough:** [Who is a customer? How do I add one?](../user_side_doc/auctioneer-client/add-customer.md)

---

## Business meaning

- A **customer** (called **client** in the dashboard and API) belongs to **one auctioneer** and is used for auction operations: registration, clerking, settlement, bid permissions, and seller/buyer accounting.
- A customer can act as:
  - **Buyer** — purchases lots (`isBuyer`, buyer accounting block).
  - **Consigner (seller)** — consigns property (`isConsigner`, consigner accounting block).
  - **Both** — buyer and consigner settings on the same profile.
  - **Floor bidder** — on-site buyer without a full Auction Journal bidder login (`isFloorBidder: true`); separate onboarding flow, still a buyer for auction purposes.

Customers are **not** platform bidders by default. If the email matches an existing **Bidder** account, `addClient` links `buyerRef` automatically.

---

## Dashboard (`auctioneer_dashboard_revamp`)

| Route | Component | Purpose |
|-------|-----------|---------|
| `/dashboard/clients` | `clientHome.jsx` | List, search, **Add Client**, **Onboard Floor Bidder**, import/export |
| `/dashboard/clients/create` | `BuildClient` | 3-step manual create |
| `/dashboard/clients/edit` | `BuildClient` (edit) | Update existing client |

### Add-client entry

1. **Customers** → add control opens **Add Clients** modal (`BuildOptionsPopup`).
2. **Add Client Data Manually** → `/dashboard/clients/create`.
3. **Driver’s license scanner** → extracts fields, generates `clientCode`, navigates to create with prefilled state.
4. **Onboard Floor Bidder** → modal wizard (`BuildFloorBidder`), sets `isFloorBidder: true` on submit.

### Manual create steps (`BuildClient`)

| Step | UI module | Data captured |
|------|-----------|----------------|
| 1 | `ClientInfo` | `clientCode`, name, phones, email, DL, DOB, notes, image, mailing lists, referral, ID card uploads |
| 2 | `Address` | `billTo`, `shipTo` (optional “same as billing”) |
| 3 | `Accounting` | Buyer accordion (reserved bid card, tax, bid permission, buyer premium); Consigner accordion (default vs specific commission) |

Submit: uploads images via blob service (`auctioneerClientImage`), then `POST /api/client/add`.

---

## Backend (`AJ-Main-Backend`)

| Method | Path | Handler | Auth |
|--------|------|---------|------|
| `POST` | `/api/client/generate_code` | `generateNewClientCode` | Auctioneer — `{tickerSymbol}{timestamp}` |
| `GET` | `/api/client/getNewReservedBidCard` | next reserved bid card number | Auctioneer |
| `POST` | `/api/client/add` | `addClient` | Auctioneer |
| `PATCH` | `/api/client/:refID` | update client | Auctioneer |

Routes: `app/routes/auctioneer-clients.js` → `controllers/auctioneer/client/client.js`.

### `addClient` behavior (high level)

- Sets `auctioneer` from `req.user.id`.
- Accepts `isBuyer`, `isConsigner`, `accounting`, `clientAddress`, `clientName`, identity images, `mailingLists`, etc.
- **Duplicate guard** (same auctioneer): existing `clientMail`, `buyerRef`, or `accounting.buyer.reservedBidCard` → `409`.
- **Bidder link:** if `clientMail` matches a `Bidder.Email`, sets `buyerRef` to that bidder.
- **Floor bidder path** (`isFloorBidder: true`): uses `email` for real email, sets `clientMail` to `clientCode`; rejects if email already belongs to a registered bidder.
- **DL uniqueness:** global duplicate `dlNumber` check across clients.
- On success: `clientsOfAuctioneer.create`, optional `addClientToLists` for mailing lists.

### Auto-create from bidder activity

`createOrUpdateAuctioneerClient(bidder, auctioneerId)` — when a bidder registers for an auctioneer’s auction, creates or updates a client with `isBuyer: true` and bidder-linked fields. Documented further under registration / bidder relationship (separate Q&A).

---

## Related

- [All customer creation paths](./creation-paths.md) — import, bidder registration, seller invite, floor bidder  
- [Import / export CSV](./import-export.md) — bulk import and sample download  
- [Fields](./fields.md) — model flags and accounting shape  
- [Mailing list](./mailing-list.md)  
- [Floor bidder (user)](../user_side_doc/auctioneer-client/floor-bidder/index.md) — dedicated floor-bidder guide (stub may exist)
