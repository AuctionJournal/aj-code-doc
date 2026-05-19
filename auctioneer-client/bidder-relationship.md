[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Bidder vs auctioneer client (customer)

A **Bidder** is a platform-wide account on Auction Journal. An **auctioneer client** (`clientsOfAuctioneer`) is that auctioneer’s CRM record for a person or company used in **that auctioneer’s** auctions. The two are related but not the same object.

**User guide:** [What is the relationship between a bidder and a customer of an auctioneer?](../user_side_doc/auctioneer-client/bidder-relationship.md)

---

## Core relationship

| Concept | Scope | Typical use |
|---------|--------|-------------|
| **Bidder** | Whole platform (`Bidder` model) | Sign in on public site, verify identity, register for auctions, place bids |
| **Client (customer)** | One auctioneer (`clientsOfAuctioneer`) | Bid card, tax, buyer premium, bid permission, settlement, clerking |

- One bidder can be linked to **many clients**—one per auctioneer they interact with.
- Link field: `buyerRef` → `Bidder._id`, `clientMail` usually matches `bidder.Email`.
- Flags: `isLinkedToBidder`, `isBidderDataConnected`, `isBuyer: true` after auto-link.

---

## Automatic client creation on auction registration

When a logged-in **bidder** successfully registers for an auction, the backend ensures they exist as that auctioneer’s client **before** the registration row is finalized.

| Item | Detail |
|------|--------|
| Route | `POST /api/auction/register` |
| File | `app/routes/auction-registration.js` |
| Auth | `requireAuth`, `allowRoles("Bidder")` |
| Handler | `createAuctionRegistration` → `registration/index.js` |
| Client call | `createOrUpdateAuctioneerClient(bidder, auction.Auctioneer._id)` |
| Implementation | `auctioneer/client/client.js` |

### `createOrUpdateAuctioneerClient` behavior

**Lookup:** `(auctioneer, clientMail === bidder.Email)`.

**If client exists** — update:

- `isBuyer: true`
- `buyerRef: bidder._id`
- Photo and identity fields from bidder `identityVerification`

**If new** — create:

- `clientCode`: `ACCL{timestamp}`
- Name, phone, DL, address from bidder profile
- `isBuyer: true`, `isLinkedToBidder: true`, `isBidderDataConnected: true`
- `buyerRef`, `clientMail: bidder.Email`
- `clientAddress.billTo` / `shipTo` from bidder address

**Registration then uses the client record:**

- **Reserved bid card** — `client.accounting.buyer.reservedBidCard` if set; else next available number for the auction.
- **Bid permission** — `buyerPermissionStatus`, cap, notes; applied on `createAuctionRegistration`. User guide: [bid permission](../user_side_doc/auctioneer-client/bid-permission.md).
- Registration payload stores `client: client._id` on `AuctionRegistration`.

See also: [Creation paths](./creation-paths.md) § bidder registers for auction.

---

## Auctioneer-specific configuration

After the client exists (auto or manual), the auctioneer configures **that client profile** in the dashboard—not the global bidder account:

| Area | Stored on client | Used when |
|------|------------------|-----------|
| Buyer accounting | `accounting.buyer` — reserved bid card, tax, buyer premium | Registration, clerking, settlement |
| Bid permission | `buyerPermissionStatus`, `buyerPermission`, notes, `isShareContact` | Registration approval path; `updateBidPermission` API |
| Consigner accounting | `accounting.consigner` | Seller-side rules (if consigner) |

**Bid permission update (auctioneer):**

- Route: `POST` (see `auction-registration.js`) → `updateBidPermission` in `registration/bid-permission.js`
- Updates both `clientsOfAuctioneer` and `AuctionRegistration`; can affect **bidder score** when `buyerRef` is set.

Manual add with same email: `addClient` sets `buyerRef` if a bidder with that email exists (`findBidderByMail`).

---

## Data sync direction

| Direction | Behavior |
|-----------|----------|
| **Bidder → linked clients** | Bidder profile name/photo updates propagate to all `clientsOfAuctioneer` with matching `buyerRef` (`bidder/profile/index.js` `updateMany`). Identity verification updates similar (`updateIdentityVerification.js`). |
| **Auctioneer client → bidder** | Client edits do **not** write back to the `Bidder` document. |
| **Registration → client** | `createOrUpdateAuctioneerClient` refreshes buyer link and identity snapshot on each new auction registration. |

---

## What this is not

- **Floor bidder** client (`isFloorBidder: true`) — may use `clientMail = clientCode` and separate email field; not the same as a linked platform bidder (see floor-bidder flows).
- **Manual client** without bidder email match — no `buyerRef` until email matches or bidder registers.
- **Seller-only** clients — may exist without ever being a bidder.

---

## Related

- [Fields](./fields.md) — `buyerRef`, linkage flags  
- [Building / add client](./build.md)  
- [Creation paths](./creation-paths.md)  
- Auction registration module: `auction/registration.md` _(if documented)_
