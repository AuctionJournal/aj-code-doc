[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Ways customers (clients) are created

An auctioneer’s **customers** are stored as **`clientsOfAuctioneer`** records, each owned by one auctioneer. Several flows create or update them.

**User summary:** [In what ways are customers created?](../user_side_doc/auctioneer-client/creation.md)

---

## Overview

| # | How | Who starts it | Backend entry | Client row created when |
|---|-----|---------------|---------------|-------------------------|
| 1 | [Manual add](./build.md) | Auctioneer dashboard | `POST /api/client/add` | On submit |
| 2 | CSV import | Auctioneer dashboard | `POST /api/auctioneer/clients/import` | After import succeeds |
| 3 | Bidder registers for auction | Bidder (public) | `createOrUpdateAuctioneerClient` from auction registration | On successful auction registration |
| 4 | Seller invite → self-register | Auctioneer invites; seller completes link | `POST /api/auctioneer/client/invite` then `POST /api/auctioneer/client/self-register` | On self-register submit (not on invite alone) |
| 5 | Floor bidder onboarding | Auctioneer dashboard | `POST /api/client/add` with `isFloorBidder: true` | On submit |

---

## 1. Manual add

- **UI:** Customers → **Add Clients** → manual or DL scan → `/dashboard/clients/create` → `POST /api/client/add`.
- **Details:** [build.md](./build.md).

---

## 2. CSV import

- **Details:** [Import and export customers (CSV)](./import-export.md)
- **UI:** Customers → **Import Clients** (`ExportImportClient`) — upload CSV, map columns, preview, optional overwrite.
- **APIs** (`auctioneer-clients.js`):
  - `GET /api/auctioneer/clients/download/sampleCSV` — sample file
  - `POST /api/auctioneer/clients/import` — `upload.single("file")`, body: `mapping` (JSON column indices), `isOverwrite`
  - `POST /api/auctioneer/clients/import/report` — error report for failed rows
- **Handler:** `clientCSV.importClientFromCSV` — parses CSV, builds payloads with **`isBuyer: true`** and **`isConsigner: true`** for imported rows, bulk insert with duplicate checks on `clientCode` / `clientMail` per auctioneer.
- **Export (not create):** `POST /api/clients/download` exports existing clients.

---

## 3. Automatic client from bidder auction registration

When a **Bidder** successfully registers for an **auction**, the backend links them to that auction’s auctioneer as a client.

- **Call site:** `auctionOperations/registration/index.js` — after registration checks pass:

```javascript
createOrUpdateAuctioneerClient(bidder, auction?.Auctioneer?._id)
```

- **Logic:** `auctioneer/client/client.js` → `createOrUpdateAuctioneerClient`:
  - If client exists for `(auctioneer, clientMail === bidder.Email)`: update → `isBuyer: true`, `buyerRef`, identity/photo from bidder.
  - Else: **create** with `isBuyer: true`, `isLinkedToBidder: true`, `isBidderDataConnected: true`, name/phone/address from bidder profile, generated `clientCode` prefix `ACCL{timestamp}`.

This is how platform **bidders** become **customers** of the auctioneer running that sale. Full relationship and per-auctioneer settings: [Bidder vs client](./bidder-relationship.md).

---

## 4. Seller invite and self-register

Auctioneer invites a **seller** (consigner) by email; the invitee completes onboarding via a public link. Client record is created only when they submit self-registration.

**User guides:** [Invite seller](../user_side_doc/auctioneer-client/invite-seller.md) (auctioneer) · [Seller self-register](../user_side_doc/auctioneer-client/seller-self-register.md) (invitee)

### Invite (auctioneer, authenticated)

| Item | Detail |
|------|--------|
| Route | `POST /api/auctioneer/client/invite` |
| Auth | `requireAuth`, `allowRoles("Auctioneer")` |
| Handler | `app/controllers/auctioneer-clients/build/index.js` → `inviteAuctioneerClient` |
| Body | `{ email }` (lowercased/trimmed) |
| Checks | `400` if `clientMail` already exists for this auctioneer |
| Action | `clientCode` = `{tickerSymbol}{timestamp}`; JWT payload `{ clientCode, auctioneer, clientMail }`; encrypt JWT; email via `auctioneerClientInviteEmailContent` |
| DB | **Does not** `create` client on invite (`AuctioneerClients.create` commented out) |
| Email link | `{AUCTIONEER_WEBSITE_URL}/onboard-client?token=…&clientCode=…&email=…&auctioneerId={AuctioneerID}` — template: `emailContentGenerator.auctioneerClientInviteEmailContent` |
| Token TTL | `JWT_EXPIRATION_IN_MINUTES` (UI success message: 24 hours) |

**Dashboard:** `clientHome.jsx` → **Onboard Seller** → `InviteNewClient` (`src/Components/Client/InviteNewClient`) → `inviteClient` in `lib/api/clients/build-client.js`.

### Self-register (public, no JWT auth on route)

| Item | Detail |
|------|--------|
| Route | `POST /api/auctioneer/client/self-register` |
| Auth | None on route |
| Handler | `auctioneerClientSelfRegister` (same build controller) |
| Body | `{ token, client }` — frontend maps `clientName`, `clientAddress.billTo` / `shipTo` from form |
| Token | `decrypt` + `jwt.verify`; `TokenExpiredError` → `400` “Request expired! Please contact auctioneer.” |
| Action | Merges decoded `clientCode`, `auctioneer`, `clientMail` with submitted `client` → `AuctioneerClients.create` (no explicit `isBuyer` / `isConsigner` in handler; model defaults `false`) |
| Required on form (UI) | `dlNumber`, `firstName`, `lastName`, `phone1` (+1 format), billing address; email read-only from query |

**Public UI:** `AppRoutes` `/onboard-client` → `clients-self-join.page.jsx` → `InvitedClientSelfJoin` — 2 steps (contact/identity + addresses); images via `imageUploader` + `clientSelfRegister`; invalid/missing query params → “Invalid Request”.

### Email positioning vs roles

Invite email copy positions the invitee as a **seller/consigner** for that auctioneer’s company. Post-create role flags depend on stored document; auctioneer typically configures **consigner** (and buyer if needed) on the client profile in dashboard ([build.md](./build.md)).

---

## 5. Floor bidder

- **UI:** Customers → **Onboard Floor Bidder** → `BuildFloorBidder` wizard → same `createAuctioneerClient` / `POST /api/client/add` with **`isFloorBidder: true`**.
- **Backend:** `addClient` floor path — `clientMail` set to `clientCode`, real email in `email`; rejects if email matches existing **Bidder**.
- **User doc:** [Who is a floor bidder? How do I onboard one?](../user_side_doc/auctioneer-client/floor-bidder/index.md).

---

## Related modules

- [Fields](./fields.md) — `isBuyer`, `isConsigner`, `isFloorBidder`, `buyerRef`
- [Mailing list](./mailing-list.md) — can attach on manual create or after import
