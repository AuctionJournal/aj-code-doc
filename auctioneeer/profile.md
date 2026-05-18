[Auction Journal](../index.md) · [Auctioneer](index.md)

# Auctioneer profile (dashboard)

Signed-in auctioneers maintain **profile text**, **profile photo**, and **login password** from the auctioneer dashboard. Updates affect how the business appears on **public auctioneer pages** and on documents that use registration profile data (for example settlement PDF company blocks — see [Invoice details](../auctioneer-misc/invoice-details.md)).

**User-facing guide:** [`user_side_doc/auctioneeer/profile.md`](../user_side_doc/auctioneeer/profile.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Who** | Authenticated **Auctioneer** role only. |
| **Where** | Dashboard **MY PROFILE** → `/dashboard/profile`. |
| **Profile photo** | Also editable from the **sidebar avatar** (`ProfilePhoto`); same S3 + photo URL flow. |
| **Password** | Shared `changePassword` endpoint for auctioneer and bidder; requires correct **old password**; sends confirmation email. |
| **Public visibility** | Bio fetch (`fetchAuctioneerBio`) exposes curated fields for public auctioneer detail; keep profile accurate for bidder trust. |
| **Not on profile page** | License number/photo, `AuctioneerID`, city/state/ZIP/country (set at registration) are **not** edited in revamp profile form — see [Registration](./registration.md). |

---

## Editable in revamp (profile form)

| Field | Required in UI | Notes |
|--------|----------------|-------|
| `CompanyName` | Yes | Auction company name |
| `FirstName`, `LastName` | Yes | Contact person |
| `StreetAddress` | Yes | Street line only in edit form |
| `Phone` | Yes | UI: `+1` + 10 digits (`^\+1\d{10}$`) |
| `Email` | Yes | Lowercase in UI validation |
| `Website` | Yes in UI | URL format when provided |
| `AuctioneerBio` | Yes | Public bio text |
| `facebook`, `youtube`, `instagram`, `linkedin` | Optional | Must match platform domain when non-empty; empty → `null` |
| `Photo` | Separate flow | Upload/remove via `ProfilePhoto`, not main edit form |

**Read-only in profile view (shown, not in edit form):** `State`, `ZipCode` (with street in address line). `City` / `Country` are on the model but not shown as separate edit inputs in revamp.

**Backend edit API also accepts (not in revamp edit UI):** `AlternateContact`, `Photo` on PATCH body — validators in `validateAuctioneerEditProfile`.

---

## Not editable via profile page

| Field / area | Where set |
|--------------|-----------|
| `City`, `State`, `Country`, `ZipCode` | [Registration](./registration.md) step 1 |
| `AuctioneerLicensceNo`, `AuctioneerLicenscePhoto` | Registration step 2 |
| `AuctioneerID`, `tickerSymbol` | System / registration |
| Phone verification flags, email verification OTP fields | Auth lifecycle |
| Stripe, formulas, invoice details | Other modules |

---

## Dashboard (`auctioneer_dashboard_revamp`)

| Piece | Path / route |
|-------|----------------|
| Page | `Pages/profile.page.jsx` — heading **My profile** |
| Route | `/dashboard/profile` |
| Nav | Sidebar **MY PROFILE** (`navigationLinks.js`) |
| Profile text | `Components/Auctioneer/Profile/index.jsx` — view / **Edit Profile** / **Save & Update** |
| Password | `Components/Auctioneer/ChangePassword/index.jsx` — **Account Info** |
| Photo | `Components/Auctioneer/ProfilePhoto/index.jsx` — sidebar avatar edit icon |

**API client:** `lib/api/profile/index.js`

---

## API (`AJ-Main-Backend`)

| Action | Method | Path | Handler |
|--------|--------|------|---------|
| Load profile | GET | `/api/auctioneerProfile` | `auctioneerProfile` |
| Edit profile | PATCH | `/api/auctioneer/profile/edit` | `editAuctioneerProfile` + `validateAuctioneerEditProfile` |
| Set photo URL | POST | `/api/uploadAuctioneerProfile` | `uploadAuctioneerProfile` + `validateuploadPhoto` |
| Remove photo | POST | `/api/removeProfilePhoto` | `removeProfilePhoto` — body `{ Photo: null, reqType: 1 }` auctioneer |
| Change password | PUT | `/api/changePassword` | `changePassword` — `{ oldPassword, newPassword }` |
| Public bio | POST | `/api/fetchAuctioneerBio` | `fetchAuctioneerBio` — body `{ Auctioneer }` (ObjectId) |

**Controller:** `app/controllers/auctioneer/profile/index.js`  
**Routes:** `app/routes/auctioneer.js` (profile + invoice + locations); `removeProfilePhoto` on `app/routes/bidder.js`.  
**Validators:** `app/controllers/profile/validators/validateEditProfile.js`

### Profile edit payload (server)

Accepts PascalCase or camelCase for several keys, e.g. `CompanyName` / `companyName`, `Website` / `website`. Updates: company name, names, photo URL, street, phone, alternate contact, email, website, bio, social URLs. Sensitive fields stripped from GET/PATCH responses (`password`, OTP fields, etc.).

### Photo upload flow

1. Revamp uploads image via blob API: `uploadS3Blob("auctioneerProfileImage", { base64, blobName, blobDetails: { auctioneerId } })`.
2. POST returned S3 URL to `uploadAuctioneerProfile` as `Photo`.
3. Remove: `removeProfilePhoto` with `reqType: 1`.

---

## Validation summary

| Layer | Profile text | Password |
|-------|----------------|----------|
| **Revamp (yup)** | Company, names, address, phone `+1`×10, email lowercase, bio, website URL, social domain checks | 8+ chars, upper, lower, number, special; confirm match |
| **Backend (express-validator)** | Optional fields if sent; phone length 12–14 with `+1`; email format; bio non-empty; website URL; social nullable URLs | Old + new password required; bcrypt compare old |

---

## Related

- Field reference: [Fields](./fields.md)
- Registration (initial values): [Registration](./registration.md)
- Public consumption: `auctionjournal-public` auctioneer / listing pages using bio and branding fields
