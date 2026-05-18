[Auction Journal](../index.md) · [Auctioneer Miscellaneous](index.md)

# Auctioneer text and bid-increment templates

Reusable **templates** let an auctioneer store standard copy and bid-increment tables once, then apply them when **building or editing an auction**. Templates are **not** linked to auctions by ID: applying a template **copies** values into the auction document. **Editing or deleting a template does not change** text or increments already saved on existing auctions (draft, live, or closed).

**User-facing guide:** [`user_side_doc/auctioneer-misc/templates.md`](../user_side_doc/auctioneer-misc/templates.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Scope** | Per auctioneer (`auctioneer` on `template` collection). |
| **Purpose** | Faster auction setup; consistent legal/notice/email copy and increment ladders across sales. |
| **Apply** | UI copies template field values into auction form state; auction save persists on `auctionDetails` (or equivalent auction fields). |
| **Save from auction** | After changing a field during auction build, **Save as Template** creates a new library entry (duplicate content rejected). |
| **Edit / delete** | Updates or removes the library record only; **no back-propagation** to auctions that already used an older version. |
| **Duplicates** | Create rejects identical `data` for the same `templateType` (JSON compare). |
| **Not the same as** | `createAuctionTemplate` in `build-auction.js` — that name is the **initial auction draft shell** API, not these miscellaneous templates. |

---

## Template types

| UI label (`templateFor`) | API `templateType` | Mongo field | Auction field(s) | Build UI section |
|--------------------------|-------------------|-------------|------------------|------------------|
| Terms & Conditions | `auction-tnc` | `termsAndCondition` `{ title, description }` | `TermsAndCondition` | Auction Info |
| Short BP Explanation | `auction-sbpe` | `shortBPExplanation` | `ShortBPExplanation` | Auction Info |
| Bidding Notice | `auction-bidding-notice` | `biddingNotice` | `biddingNotice` | Notices |
| Auction Notice | `auction-notice` | `auctionNotice` | `auctionNotice` | Notices |
| Email Body | `auction-reg-email` | `email` `{ title, subject, body }` | `emailSubject`, `emailBody` | Registration |
| Bid Increment | `auction-bid-increment` | `incrementPattern[]` `{ amount, increment }` | `bidIncrement` | Bid Increment |

**Bidder-facing use (after auction save):** copy on the auction record is shown where the product renders those fields — e.g. `auctionjournal-public` single auction (terms, short BP, bidding notice), registration popup (terms), registration emails (subject/body from auction). Increments drive next-bid logic via `incrementPattern` on the saved auction.

---

## Data model

**Model:** `template` (`app/models/template.js`)

- `templateType` enum: six values above.
- One content sub-document per row (only the field matching `templateType` is populated).
- Index: `{ auctioneer: 1, templateType: 1 }`.
- Timestamps enabled.

---

## API (`app/routes/auctioneer-templates.js`)

Auth: JWT, role **Auctioneer**.

| Method | Path | Body / query | Notes |
|--------|------|--------------|--------|
| GET | `/api/auctioneer/templates` | `?templateType=` | List for current auctioneer |
| POST | `/api/auctioneer/template` | `{ templateType, data }` | Create; 400 duplicate / invalid type |
| PATCH | `/api/auctioneer/template` | `{ templateId, templateType, data }` | Edit |
| DELETE | `/api/auctioneer/template` | `{ templateId }` | Owner check; 404 if not owner |

**Controller:** `app/controllers/auctioneer-templates/index.js` — `templateKeys` maps `templateType` → document field name.

---

## Dashboard (`auctioneer_dashboard_revamp`)

### Miscellaneous hub

`Pages/Miscellaneous/miscellaneous.page.jsx` — tiles route to:

| Tile | Route |
|------|--------|
| Terms & Conditions | `/dashboard/miscellaneous/templates/terms` |
| Short BP Explanation | `/dashboard/miscellaneous/templates/short-bp` |
| Bidding Notices | `/dashboard/miscellaneous/templates/bidding-notice` |
| Auction Notices | `/dashboard/miscellaneous/templates/auction-notice` |
| Email Body | `/dashboard/miscellaneous/templates/email-body` |
| Bid Increment | `/dashboard/miscellaneous/templates/bid-increment` |

Pages use `Components/Templates/SharedTemplate` (list, create, edit, delete) or `BidIncrement` for increment templates.

### Shared components (`Components/Templates/`)

| Component | Role |
|-----------|------|
| `Hooks/index.jsx` | `useTemplates(templateFor)` → GET by mapped type |
| `SharedTemplate` | Paginated list; **Create New** / **Edit** / **Delete** |
| `SharedTemplate/BuildTemplate` | Dialog create/edit (title + description, or email title/subject/body) |
| `SharedTemplateSelector` | **Use Template** popup during auction build |
| `SaveTemplateButton` | **Save as Template** from current field value |
| `BidIncrement` | Increment ladder CRUD in Miscellaneous |

**API client:** `lib/api/templates/index.jsx` → backend paths above.

### Auction build integration

| File | Templates used |
|------|----------------|
| `BuildAuction/AuctionInfo` | Terms & Conditions, Short BP Explanation |
| `BuildAuction/Notices` | Bidding Notice, Auction Notice |
| `BuildAuction/Registration` | Email Body (subject + rich-text body) |
| `BuildAuction/BidIncrement` | Bid Increment pattern |

Flow:

1. **Select from template** (icon or button) → popup → **Use Template** → `onTemplateUse` sets auction state (copy only).
2. User edits field → **Save as Template** appears → POST new template (or duplicate error).

Field-level disable when auction phase locks editing uses `edit_auction_disable_context` (same as other build fields).

---

## Related backend (auction persistence)

Auction header fields: `app/models/auctionDetails.js` — `TermsAndCondition`, `ShortBPExplanation`, `biddingNotice`, `auctionNotice`, email fields, `bidIncrement` / increment usage in live webcast helpers.

No foreign key from auction → `template` document.

---

## Edge cases

- **Duplicate template:** POST returns `Duplicate template data`.
- **Bid increment:** `data` must be an **array** for `auction-bid-increment`.
- **Email template save from build:** requires non-empty body (and subject when saving from Registration).
- **Formula templates:** not in this system; formulas are a separate Miscellaneous module ([formulas.md](formulas.md)).
