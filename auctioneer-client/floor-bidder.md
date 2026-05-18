[Auction Journal](../index.md) · [Auctioneer Client](index.md)

# Floor bidder

A **floor bidder** is an auctioneer **client** (`clientsOfAuctioneer` with `isFloorBidder: true`) who participates **in person (offline)** at a sale. They are usually registered on **Onsite With Live Webcast** auctions. The auctioneer staff **enters bids for them** during live bidding and **checks them in** to the auction from the dashboard.

**User guide:** [Who is a floor bidder? How do I onboard one?](../user_side_doc/auctioneer-client/floor-bidder/index.md)

---

## Business rules

| Topic | Rule |
|--------|------|
| **Identity** | Floor bidder is a **customer of one auctioneer**, not a platform bidder account (usually no AJ bidder login). |
| **Buyer role** | Treated as a **buyer** for clerking, settlement, and bid card assignment. |
| **Auction type** | Auction registration as floor bidder is only when `AuctionType === "Onsite With Live Webcast"`. |
| **Registration window** | `onboardFloorBidder` requires current date within auction `startDate`–`endDate`. |
| **Bidding** | Bids are placed by the **auctioneer** (live webcast / clerking), not by the person logging into the public bidder site. |
| **vs online bidder** | If email already belongs to a registered **Bidder**, `addClient` with `isFloorBidder` is rejected; use online registration or invite paths in check-in. |

---

## Two-step onboarding (typical)

### Step A — Create floor bidder client (CRM)

| Item | Detail |
|------|--------|
| UI | **Customers** → **Onboard Floor Bidder** → `BuildFloorBidder` wizard |
| API | `POST /api/client/add` with `isFloorBidder: true` |
| Handler | `auctioneer/client/client.js` → `addClient` |

**Floor-specific `addClient` behavior:**

- Sets `isFloorBidder: true`.
- `email` = valid `clientMail` if provided; `clientMail` stored as **`clientCode`** (internal key).
- Rejects if `Bidder` exists with same email.

Wizard steps: Client info → Address → Accounting (buyer premium/tax) → Additional info.

### Step B — Register client on an auction (check-in)

| Item | Detail |
|------|--------|
| UI | Auction **Registration** tab → **Check-in Floor Bidder** (only for **Onsite With Live Webcast**, auction not closed) |
| API | `POST /api/auctioneer/auction/register/floor-bidder` |
| Route file | `app/routes/auction-registration.js` |
| Auth | `requireAuth`, `allowRoles("Auctioneer")` |
| Handler | `onboardFloorBidder` in `registration/index.js` |

**Body (main path):** `{ clientId, auctionId }` — links existing floor-bidder client to auction.

**Creates `AuctionRegistration` with:**

- `isFloorBidder: true`, `client`, `bidCardNumber`, `bidPermissionStatus: "Approved"`, `noteToAuctioneer: "Floor Bidder"`.
- `bidder` field = `client.buyerRef || client._id` (no platform bidder required).

Sets `AuctionLiveWebcast.isFloorBidderAvailable: true` for the auction.

**Alternate check-in branches** (`isABidder` / `willBeABidder` + `emailToInvite`):

- Invite existing AJ bidder to register for auction (email).
- Invite email to become bidder later (registration request email).
- Not the classic offline floor-bidder path but exposed in same dialog.

---

## During the sale

- **Live webcast / bidding:** auctioneer selects floor bidder and records bids (e.g. `POST /api/auctioneer/auction/floorbidder/bids` from dashboard).
- **Clerking:** lots can be sold to floor bidder (`winningFloorBidder`, `floorBidderId` in clerking controller).
- **Registration list:** floor bidders appear in auction registration views / `FloorBiddersList`; filter `isFloorBidder` on client fetch.

---

## Data model flags (`clientsOfAuctioneer`)

- `isFloorBidder: true`
- `email` — contact email (display/search on client list)
- `clientMail` — often equals `clientCode` for floor records
- `buyerRef` — usually null unless later linked

`AuctionRegistration.isFloorBidder: true` distinguishes registration from online bidder self-registration (`POST /api/auction/register`).

---

## Related APIs (reference)

| Method | Path | Purpose |
|--------|------|---------|
| `POST` | `/api/client/add` | Create floor bidder client |
| `POST` | `/api/auctioneer/auction/register/floor-bidder` | Check in to auction |
| `POST` | `/api/auctioneer/auction/floorbidder/registrations` | List floor bidder registrations |
| `POST` | `/api/auctioneer/auction/registrations/floorbidder` | Floor bidder registration details |
| `POST` | `/api/auctioneer/auction/floorbidder/bids` | Place bid as floor bidder |

---

## Related docs

- [Bidder vs client](./bidder-relationship.md)  
- [Building / add client](./build.md)  
- [Creation paths](./creation-paths.md)  
- Live webcast: `auction/onsite-livewebcast/` _(module docs)_
