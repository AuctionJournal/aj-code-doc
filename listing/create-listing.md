[Auction Journal](../index.md) · [Listing](./index.md)

# Create listing (BuildListing)

Auctioneers create **listings** in the dashboard via a type picker and a four-step wizard. Listings can be **saved as draft** or **published**; publish either goes live for free (eligible auctioneers) or routes through a **payment option** modal and Stripe checkout.

**User guide:** [What is a listing? How do I create one?](../user_side_doc/listing/create-listing.md)

---

## What is a listing? (business)

A **listing** is a public-facing promotion for an upcoming auction event on Auction Journal. It is **not** the same as an **Auction** record in the CRM auction module.

| Aspect | Meaning |
|--------|---------|
| Purpose | Help bidders discover auctions (date, location, media, notices) on the public site |
| Visibility | `isPublished: true` → public; drafts visible only under **Manage Listings** |
| Lifecycle | Draft → Published → Live (until `AuctionDate`) → Past |
| Bidder actions | On-site type → bid pass; other types → listing callback request (see [Listing index](./index.md)) |

Field reference: [Fields](./fields.md).

---

## Frontend entry

| Item | Location |
|------|----------|
| Route | `/dashboard/listing/create` |
| Page | `src/Pages/Listing/create.page.jsx` |
| Type picker | `Components/Molecules/TypeSelection` — five auction types |
| Wizard | `Components/Listing/BuildListing/index.jsx` (`ListingForm`) |
| Steps | `step1.jsx` … `step4.jsx` |
| Payment modal | `BuildListing/PaymentOption/index.jsx` |
| Edit flow | `/dashboard/listing/edit` — same `ListingForm` with `isEdit` |

Nav: **Listings** → **Create** (`navigationLinks.js`).

---

## Wizard steps (order)

| Index | File | UI title | Main fields |
|-------|------|----------|-------------|
| 0 | (create page) | Select type of listing | OnSite, Live Webcast, Online Timed, Online Absolute, Live Webcast with OnSite |
| 1 | `step1.jsx` | Auction Details | `AuctionType` (disabled), `NameOfProduct`, `AuctionTitle`, `AuctionCategory`, `CategoryDetails`, `AuctionDate`, `AuctionTime` |
| 2 | `step2.jsx` | Auction Location | `Address1`, `Address2`, `Zip`, `City`, `State`, `Country` (zip auto-fill) |
| 3 | `step3.jsx` | Auction Media | `uploadPhoto[]`, `imagePathId`; dual galleries if category **Estate With Real Estate** |
| 4 | `step4.jsx` | Auction Description | `ProductDescription`, `BiddingNotice`, `AuctionNotice`, `TermsAndCondition` (rich text) |

**Date rule:** `AuctionDate` minimum is today + 2 days; step 1 merges date + time into one `AuctionDate` datetime on Next / Save as Draft.

**Media upload:** `uploadS3Blob` / blob API, type `listingImage`, path keyed by `imagePathId` (`ListingID` or `LS{timestamp}`).

---

## Save as draft

**UI:** Orange **Save as Draft** on steps 1–4 when `!isPublished`.

**Handler:** `ListingForm` mutation → `editListingDraft` if `isEdit` or `data._id`, else `createListingDraft`.

**Result:** Merges `res.data` into form state, snackbar *Successfully saved as draft.*, navigates to `/dashboard/listing/manage`.

Draft APIs do **not** check free-listing eligibility; `price` is set from `appsetting.listingPrice` on the server.

---

## Publish flow (`handleSubmithandler`)

Triggered from step 4 **Publish** (or **Save Changes** when already published).

| Condition | API / UI |
|-----------|----------|
| `data.isPublished` | `editListing` → navigate to manage (no payment) |
| `user.isEligibleForFreeListing` | `createListing` → `Success` screen → manage |
| Not eligible | `createListingDraft` or `editListingDraft` → open `PaymentOptions` modal |

### Payment modal (`PaymentOptions`)

- **Create a free listing** → `Link` to `/dashboard/listing/free-listing` (tag verification; see [Free Listing Eligibility](../auctioneeer/free-listing-eligibility.md)).
- **Pay $price** → `navigate` to `/dashboard/billings/payment/checkout` with:

```js
{
  product: "Listing",
  productType: "listing",
  price: listing?.price,
  reference: listing?._id,
  productId: listing?.ListingID,
}
```

Checkout: `POST /api/payment` → Stripe → `POST /api/payment/confirm` sets listing `isPublished: true` for `productType === "listing"`.

**Note:** Paid publish from the wizard **does not** call `draft/publish`; it relies on payment confirmation. `publishListingDraft` is used from **Manage Listings** for eligible free users only; ineligible users get the same payment modal on error.

---

## API map (auctioneer listing build)

| Client function | Method | Path |
|-----------------|--------|------|
| `createListingDraft` | POST | `/api/auctioneer/listing/draft/create` |
| `editListingDraft` | POST | `/api/auctioneer/listing/draft/edit` |
| `fetchListingDrafts` | GET | `/api/auctioneer/listing/draft` |
| `publishListingDraft` | POST | `/api/auctioneer/listing/draft/publish` |
| `createListing` | POST | `/api/auctioneer/listing/create` |
| `editListing` | POST | `/api/auctioneer/listing/edit` |
| `fetchAuctionCategories` | GET | `/api/fetchAuctionCategory` |

Routes: `AJ-Main-Backend/app/routes/listing-build.js`.  
Controllers: `controllers/auctionListing/listing-draft.js`, `listing-build.js`.  
Payment publish: `controllers/paymentAndBilling/payment.js` (`confirmPayment`).

### Backend publish rules (summary)

| Path | Requires `isEligibleForFreeListing` | Sets `isPublished` |
|------|--------------------------------------|--------------------|
| `listing/create` | Yes | Yes, `price: 0` |
| `draft/publish` | Yes | Yes, `price: 0` |
| `draft/publish` (ineligible) | — | 400 + `isEligibleForFreeListing: false` |
| Payment confirm (`listing`) | No | Yes on `product.reference` |

Geocoding on free publish uses address fields; failure blocks publish.

---

## Related docs

- [Listing module index](./index.md) — lifecycle, bidder intents, manage/export
- [Free listing (user)](../user_side_doc/listing/free-listing.md)
- [Free listing eligibility (dev)](../auctioneeer/free-listing-eligibility.md)
