[Auction Journal](../index.md) · [Auctioneer Miscellaneous](index.md)

# Invoice details (Miscellaneous)

Invoice details are the auctioneer’s **receipt and invoice branding** settings: business contact information, receipt title and message, and options for billing/shipping address requirements. Data is stored once per auctioneer and used when generating **auction settlement invoices** (PDF).

**User guide:** [`user_side_doc/auctioneer-misc/invoice-details.md`](../user_side_doc/auctioneer-misc/invoice-details.md)

---

## Business rules

| Topic | Behavior |
|--------|----------|
| **Required for auctions** | `auctioneerEligiblityToCreateAuction()` checks `AcMiscInvoiceDetails.exists({ auctioneer })`. Missing record blocks draft auction create (`result.invoice` false). |
| **One record per auctioneer** | Unique `auctioneer` on `acMiscInvoiceDetails`. |
| **First visit** | No record → UI shows empty form (create). After save → read-only summary with **Edit**. |
| **Settlement PDF** | `auctionSettlementInvoicePDF` loads invoice details; **`receiptTitle`** becomes centered header on buyer/seller settlement invoice PDF (`invoiceDetailsTitle`). |
| **Company block on PDF** | Auctioneer **registration profile** (company name, street, city, phone, email, logo) — not necessarily `address1` from invoice details. |
| **Phone format (UI)** | Revamp validates US: `+1` + 10 digits (e.g. `+11234567890`). |

---

## Data model

**Model:** `acMiscInvoiceDetails` (`app/models/acMiscInvoiceDetails.js`)

| Field | Purpose |
|-------|---------|
| `address1`, `address2` | Billing/office address lines |
| `city`, `state`, `zip`, `country` | Location (ZIP may auto-fill city/state in UI) |
| `phone`, `website` | Contact on file |
| `receiptTitle` | Header text on settlement invoice PDF |
| `receiptMsg` | Custom receipt message (stored; shown in dashboard summary) |
| `isReqBillingAddress` | Whether billing address is required on settlement paperwork |
| `isReqShippingAddress` | Whether shipping address is required on settlement paperwork |

---

## Dashboard UI

| Item | Value |
|------|--------|
| Route | `/dashboard/miscellaneous/invoices` |
| Page | `Pages/Miscellaneous/invoice-details.page.jsx` |
| Component | `Components/Miscellaneous/InvoicesDetails/index.jsx` |
| API client | `lib/api/miscellaneous/invoice.js` |

### Flow

1. `GET /api/auctioneer/invoice` — if `data` null, show create form.
2. **Save** → `POST /api/auctioneer/invoice` (create) or `PATCH /api/auctioneer/invoice/:refId` (update).
3. View mode shows combined address, phone, website, receipt title, receipt message.

---

## Backend APIs

**Routes:** `app/routes/auctioneer.js`  
**Controller:** `app/controllers/auctioneer/miscellaneous/invoiceDetails.js`  
**Auth:** `requireAuth`; create/update require `allowRoles("Auctioneer")`.

| Method | Path | Purpose |
|--------|------|---------|
| GET | `/api/auctioneer/invoice` | Fetch single record for logged-in auctioneer |
| POST | `/api/auctioneer/invoice` | Create invoice details |
| PATCH | `/api/auctioneer/invoice/:refId` | Update (matches by `auctioneer` in controller) |

---

## Settlement usage

- **Invoice PDF:** `app/controllers/auctionOperations/settlement/invoicePDF.js` — `GET` settlement invoice PDF uses `receiptTitle` from `AcMiscInvoiceDetails`.
- **Route:** `/api/auctioneer/auction/settlement/invoicePDF/:settlementId` (see `auction-settlement.js`).
- Settlement records include `invoiceNumber`; PDF shows lot lines, totals, payment status, buyer/seller blocks.

---

## Implementation notes

- Revamp **Require Billing Address** / **Require Shipping Address** radio groups use `defaultValue` only; confirm whether values are bound to `data` before relying on persisted flags in new dashboard builds.
- `receiptMsg` is required in frontend validation but is not referenced in `invoicePDF.js` generate path at time of writing—may appear in other settlement outputs or future PDF footer.

---

## Related documentation

- [Auction build](../auction/build.md) — invoice prerequisite
- [Auction settlement](../auction/settlement.md)
- [Formulas](formulas.md) · [Accounts](account.md)
