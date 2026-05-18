[Auction Journal](../index.md) · [Bidder](index.md)

# Bidder profile (public dashboard)

Signed-in bidders maintain **profile text**, **profile photo**, and **login password** from the bidder dashboard on `auctionjournal-public`. Name changes sync to **auctioneer client** records linked as buyers (`ClientsOfAuctioneer` where `buyerRef` matches).

**User-facing guide:** [`user_side_doc/bidder/profile.md`](../user_side_doc/bidder/profile.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Who** | Authenticated **Bidder** (JWT). |
| **Where** | **My Profile** → `/bidder/profile` (`bidder/profile/page.jsx` → `Components/Bidder/Profile`). |
| **Profile photo** | Sidebar / hamburger avatar (`Sidebar/UserProfile`) — upload, change, remove. |
| **Email** | Shown under **Account Info**; **not** updated via profile edit API (`Email` commented out in `editBidderProfile`). |
| **ID verification** | Driving licence / state ID — [Verification](./verification.md), not the profile form. |
| **Client sync** | After profile edit, `ClientsOfAuctioneer.updateMany({ buyerRef })` sets `clientName` from first/last name; photo update sets `imageProvided`. |

---

## Editable on My Profile (revamp UI)

### PROFILE INFO — `ProfileInfo`

| Field | Required in UI | Notes |
|--------|----------------|-------|
| `FirstName` | Yes | |
| `LastName` | Yes | |
| `StreetAddress` | Yes | Multiline address |
| `ZipCode` | Yes | Changing ZIP runs `fetchZipCodeDetails` to refresh **City** and **State** |
| `City` | Yes | Input **disabled** in UI; updates when ZIP changes |
| `State` | Yes | Input **disabled** in UI; updates when ZIP changes |
| `Phone` | Yes | |

### ACCOUNT INFO — `AccountInfo`

| Field | Editable? |
|--------|-----------|
| `Email` | **View only** |
| Password | **Change Password** — old, new, re-enter new (Yup rules same pattern as auctioneer dashboard) |

### Profile photo (sidebar)

| Action | API |
|--------|-----|
| Upload / change | S3 `bidderProfileImage` blob → `POST /api/uploadBidderProfile` `{ Photo: url }` |
| Remove | `POST /api/removeProfilePhoto` `{ Photo: null, reqType: 2 }` |

---

## Not editable via My Profile

| Item | Where managed |
|------|----------------|
| `Email` | Set at [Registration](./registration.md); change via support / account processes if needed |
| `BidderID` | System |
| `identityVerification` (ID images) | [Verification](./verification.md) |
| Stripe cards | [Verification](./verification.md) / verified manage screen |
| `Country` | On model; not a separate field on this edit form (ZIP helper may set country in payload internally) |

**Backend `editBidderProfile`** can also accept legacy billing/card fields (`Nickname`, `CardHolderName`, etc.) if sent — **not** exposed on current public profile UI.

---

## API (`AJ-Main-Backend`)

| Action | Method | Path | Handler |
|--------|--------|------|---------|
| Load profile | GET | `/api/bidderProfile` | `bidderProfile` |
| Edit profile | PATCH | `/api/bidder/profile/edit` | `editBidderProfile` + `validateBidderEditProfile` |
| Set photo | POST | `/api/uploadBidderProfile` | `uploadBidderProfile` |
| Remove photo | POST | `/api/removeProfilePhoto` | `removeProfilePhoto` (`reqType: 2`) |
| Change password | PUT | `/api/changePassword` | `changePassword` — body `oldPassword`, `newPassword` (auctioneer dashboard); bidder client uses `lib/api/bidder/profile.js` `updateBidderPassword` |

**Controller:** `app/controllers/bidder/profile/index.js`  
**Routes:** `app/routes/bidder.js`  
**Validators:** `app/controllers/profile/validators/validateEditProfile.js` — `validateBidderEditProfile`

---

## Client (`auctionjournal-public`)

| Piece | Location |
|-------|----------|
| Page | `src/app/bidder/profile/page.jsx` |
| Component | `src/app/Components/Bidder/Profile/index.jsx` |
| API | `src/lib/api/bidder/profile.js` — `fetchBidderProfile`, `editBidderProfile`, `updateBidderPassword`, photo helpers |
| Nav | `Bidder_Layout/navData.js` — **My Profile** |

---

## Related

- [Fields](./fields.md)  
- [Registration](./registration.md)  
- [Verification](./verification.md)  
- Forgot password: [Authentication / Forgot password](../auth/forgot-password.md)
